# Potential Improvements for the Download Feature

Based on the analysis of the download feature, several areas could be enhanced for better user experience, robustness, and performance.

### User Experience (UX) Enhancements

1.  **Selective Downloads:**
    *   **Current State:** The extension downloads all detected files at once.
    *   **Improvement:** Before starting the download, the extension could present the user with a checklist of all the files it found on the page. This would allow the user to select only the specific files they need, saving bandwidth and time.

2.  **Post-Download Actions:**
    *   **Current State:** The user receives a notification upon completion.
    *   **Improvement:** Add a "Show in folder" or "Open downloads" button in the popup after a batch is completed. This provides a more seamless workflow, allowing the user to immediately access their files without manually navigating to their downloads folder.

3.  **Improved Feedback on Skipped Files:**
    *   **Current State:** A summary message mentions that some files were skipped due to extension filtering.
    *   **Improvement:** Provide a clear, expandable list of the specific files that were skipped and why. This would help users diagnose issues if they believe a file should have been downloaded.

### Robustness and Error Handling

1.  **Retry Mechanism for Failed Downloads:**
    *   **Current State:** If a file fails to download, the error is logged, and the process moves on.
    *   **Improvement:** Implement a queue for failed downloads. The UI could show a list of failed files with a "Retry All" or individual retry buttons. This is crucial for users on unstable internet connections.

2.  **Duplicate File Handling:**
    *   **Current State:** The extension relies on the browser's default behavior for duplicate filenames (e.g., creating `file (1).pdf`).
    *   **Improvement:** Add a user setting to control this behavior. Options could include:
        *   **Overwrite:** Replace the existing file.
        *   **Skip:** Do not download the file if it already exists.
        *   **Rename:** The current default behavior.

### Performance Optimization

1.  **Concurrent File Discovery:**
    *   **Current State:** The `content.js` script processes each resource link sequentially, fetching the resource page to find the final file URL one by one.
    *   **Improvement:** The file discovery phase could be significantly accelerated by using concurrent requests (e.g., with `Promise.all` and a concurrency limit of 3-5 simultaneous fetches). This would make the "Preparing downloads…" phase much faster, especially on pages with many files.

### New Features

1.  **Per-Course Settings:**
    *   **Current State:** All settings (download path, folder structure) are global.
    *   **Improvement:** Allow users to save settings on a per-course basis. The extension could use the `courseCode` to store and retrieve specific configurations, falling back to global settings if no course-specific rules are found.

2.  **Export File List:**
    *   **Improvement:** Add a feature to export the list of discovered file URLs and names to the clipboard or a `.txt` file. This would be useful for users who want to use a different download manager or keep a record of the course files.
