# Gemini Zip Analyzer

This Python script analyzes the contents of a ZIP file by extracting it, identifying supported file types (documents, audio, images, videos), uploading these files to Google's Gemini API, and then generating a response based on the file contents and a user-provided prompt.

## Features

*   **ZIP File Processing**: Extracts contents from a user-specified ZIP file.
*   **File Tree Generation**: Displays the directory structure of the extracted files.
*   **Selective File Type Support**:
    *   Identifies and processes various document, audio, image, and video file types.
    *   File type processing can be toggled on/off (e.g., only analyze documents and images).
*   **Customizable File Types**: Easily extend or modify the list of supported file extensions and their MIME types.
*   **Google Gemini Integration**:
    *   Uploads supported files to the Gemini API.
    *   Sends the file structure, uploaded files, and a user prompt to a Gemini model for analysis.
*   **User Interaction**: Prompts the user for the ZIP file path and the analysis query.
*   **Automatic Cleanup**: Deletes the temporary directory created for extraction after processing.
*   **Error Handling**: Includes basic error handling for file operations and API interactions.

## Prerequisites

*   Python 3.7+
*   A Google Gemini API Key. You can obtain one from [Google AI Studio](https://aistudio.google.com/app/apikey) or your Google Cloud Project.

## Installation

1.  **Clone the repository (or download the script):**
    ```bash
    git clone https://github.com/YoussefElsafi/gemini-zip-analyzer.git
    cd gemini-zip-analyzer
    ```

2.  **Install the required Python package:**
    The script uses the `google-genai` library.
    ```bash
    pip install google-genai
    ```
    Other modules used (`os`, `zipfile`, `shutil`) are part of the Python standard library.

## Configuration

Before running the script, you need to configure your Gemini API Key and optionally toggle file type processing. Open the Python script (`your_script_name.py`) and modify the following lines:

1.  **Set your Gemini API Key:**
    Replace `"YOUR_GEMINI_API_KEY"` with your actual API key.
    ```python
    # --- Configuration ---
    GEMINI_API_KEY = "YOUR_GEMINI_API_KEY"  # Replace with your actual API key
    ```
    **Important**: Treat your API key like a password. Do not commit it directly to public repositories if you plan to share your code widely. Consider using environment variables or a separate configuration file for better security.

2.  **Toggle File Type Inclusion (Optional):**
    By default, all supported file categories (docs, audio, images, videos) are included. You can set these to `False` if you want to exclude certain types of files from being uploaded and analyzed.
    ```python
    # --- Options for file types (can be toggled) ---
    INCLUDE_DOCS = True
    INCLUDE_AUDIO = True
    INCLUDE_IMAGES = True
    INCLUDE_VIDEOS = True
    ```

3.  **Customize Supported File Types (Advanced - Optional):**
    The `SUPPORTED_FILE_TYPES` dictionary defines which file extensions are recognized and their corresponding MIME types. You can add or modify entries if needed.
    ```python
    # --- Customizable Supported File Types ---
    SUPPORTED_FILE_TYPES = {
        "docs": {
            ".pdf": "application/pdf",
            # ... other document types
        },
        "audio": {
            ".mp3": "audio/mp3",
            # ... other audio types
        },
        # ... and so on for image and video
    }
    ```

## Usage

1.  Ensure you have configured your `GEMINI_API_KEY` in the script.
2.  Run the script from your terminal:
    ```bash
    python main.py
    ```

3.  The script will prompt you to:
    *   **Enter the path to the ZIP file:** Provide the full or relative path to the `.zip` file you want to analyze.
        ```
        Enter the path to the ZIP file: example.zip
        ```
    *   **Enter your prompt for Gemini:** Type the question or instruction you want Gemini to process based on the contents of the ZIP file.
        ```
        Enter your prompt for gemini: Summarize the main topics covered in the documents and describe the images.
        ```

4.  The script will then:
    *   Extract the ZIP file to a temporary directory (`extracted_files`).
    *   Print the file tree structure of the extracted content.
    *   List the supported files found within the ZIP.
    *   Upload the supported files to Gemini.
    *   Send your prompt and the file information to the Gemini API.
    *   Print the response received from Gemini.
    *   Clean up by deleting the `extracted_files` directory.

## How It Works

1.  **User Input**: Gathers the ZIP file path and the user's analysis prompt.
2.  **Extraction**: Unzips the provided file into a temporary local directory named `extracted_files`.
3.  **File Tree Generation**: Scans the `extracted_files` directory and creates a textual representation of its structure.
4.  **File Discovery**: Walks through the extracted files, identifying those with extensions listed in `SUPPORTED_FILE_TYPES` and whose categories (`docs`, `audio`, etc.) are enabled by the `INCLUDE_...` flags.
5.  **File Upload**: For each supported file, it determines the MIME type and uploads it to the Gemini API using the `google.generativeai` client.
6.  **Content Generation**: It constructs a request to the Gemini API (`gemini-2.5-flash-preview-04-17` model by default) including:
    *   The user's prompt.
    *   The generated file tree structure.
    *   References to the uploaded files.
    *   A system instruction telling the AI it's analyzing a ZIP file.
7.  **Response**: The API's generated text response is printed to the console.
8.  **Cleanup**: The temporary `extracted_files` directory is removed.

## Supported File Types

The script supports a range of file types by default. You can see the full list in the `SUPPORTED_FILE_TYPES` dictionary within the script. Here's a summary:

<details>
<summary><strong>Documents</strong> (if <code>INCLUDE_DOCS = True</code>)</summary>

*   .pdf, .js, .py, .txt, .html, .css, .md, .csv, .xml, .rtf
</details>

<details>
<summary><strong>Audio</strong> (if <code>INCLUDE_AUDIO = True</code>)</summary>

*   .wav, .mp3, .aiff, .aac, .ogg, .flac
</details>

<details>
<summary><strong>Images</strong> (if <code>INCLUDE_IMAGES = True</code>)</summary>

*   .png, .jpeg, .jpg, .webp, .heic, .heif
</details>

<details>
<summary><strong>Videos</strong> (if <code>INCLUDE_VIDEOS = True</code>)</summary>

*   .mp4, .mpeg, .mpg, .mov, .avi, .flv, .webm, .wmv, .3gp
</details>

## Error Handling

The script includes `try-except` blocks to catch common issues such as:
*   File not found for the input ZIP.
*   Invalid or corrupted ZIP file.
*   Errors during file upload to Gemini.
*   Errors from the Gemini API.
Warnings or errors encountered during processing will be printed to the console.

## Limitations

*   **API Costs & Quotas**: Use of the Google Gemini API may be subject to costs and usage quotas. Be mindful of the size and number of files you process.
*   **File Size Limits**: The Gemini API has limits on the size of files that can be uploaded. Very large files might cause errors.
*   **Temporary Disk Space**: The script requires temporary disk space to extract the ZIP file. Ensure you have enough free space, especially for large archives.
*   **Internet Connection**: An active internet connection is required for interacting with the Gemini API.

## License

This project is licensed under the MIT License - see the `LICENSE` file for details (if you add one). If no `LICENSE` file is present, you might want to consider adding one (e.g., MIT is a common choice for open-source projects).
