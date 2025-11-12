# e-Disciplinas File Downloader - Chrome Extension

A Chrome extension that automatically downloads all files from e-Disciplinas course pages with intelligent organization.

## Features

- 🚀 **One-Click Download**: Download all files from a course page instantly
- 📦 **Smart Organization**: Automatic organization by custom directory and course code
-  **Automatic File Naming**: Preserves original file extensions (`.pdf`, `.docx`, etc.)
- 🔗 **Advanced URL Detection**: 9-strategy system handles complex redirect chains
- ⚙️ **Persistent Settings**: Configure download directories that persist across sessions
- 🤖 **Course Auto-Detection**: Automatically extracts course code from page
- 🎨 **Beautiful UI**: Clean, intuitive interface with settings page
- 🔍 **Enhanced Debugging**: Comprehensive console logging for troubleshooting (v1.2.0+)
- ⏱️ **Timeout Protection**: 15-second timeout prevents hanging on slow connections (v1.2.0+)
- ✅ **Manifest V3 Ready**: Modern Chrome extension architecture

## How It Works

### File Detection & Download Flow

1. **Scan Page**: Finds all resources with class `aalink stretched-link` linking to `/mod/resource/view.php`
2. **Extract URLs**: Uses 9-strategy system to reliably extract actual file URLs:
   - Direct pluginfile URLs in response
   - Full pluginfile URLs in HTML
   - Relative pluginfile paths
   - Meta refresh redirects
   - JavaScript location changes
   - Data attributes
   - Download links
   - File extensions
   - Generic pluginfile URLs
3. **Preserve Extensions**: Automatically extracts and appends file extensions from URLs
4. **Organize Files**: Applies user settings for directory structure
5. **Download**: Uses Chrome's download API with proper file organization

### Download Organization

Files are automatically organized as:
```
~/Downloads/[custom-path]/[course-code]/[filename].[ext]
```

**Examples:**
- Default: `~/Downloads/e-Disciplinas/SSC0534/Aula1.pdf`
- Custom: `~/Downloads/courses/2025/SSC0534/Slides.pptx`
- Root: `~/Downloads/SSC0534/Trabalho.docx`

## Installation

### Quick Install (Development)

1. Open Chrome and navigate to `chrome://extensions/`
2. Enable "Developer mode" (toggle in top-right corner)
3. Click "Load unpacked"
4. Select the `chrome-extension` folder
5. Extension appears in your toolbar

### First-Time Setup

1. Navigate to an e-Disciplinas course page
2. Click the extension icon
3. Click "Download All Files"
4. Files download to default: `~/Downloads/e-Disciplinas/[CourseCode]/`

### Configuration (Optional)

1. Click the ⚙️ settings button in the extension popup
2. Customize download path (e.g., `courses/2025`)
3. Toggle course folder organization on/off
4. Click "Save Settings"
5. Settings persist across browser sessions

## Usage

### Basic Download

1. Navigate to any e-Disciplinas course page
2. Click the extension icon in Chrome toolbar
3. Click "Download All Files" button
4. Files appear in `~/Downloads/e-Disciplinas/[CourseCode]/`

### Customize Settings

1. Click extension icon
2. Click ⚙️ (gear icon) or "Settings" link
3. Modify settings:
   - **Download Path**: Custom directory relative to Downloads
   - **Course Folder**: Toggle automatic course subfolder creation
4. Click "Save Settings"
5. Changes apply to all future downloads

### Configuration Examples

| Path Setting | Result Path |
|---|---|
| `e-Disciplinas` | `~/Downloads/e-Disciplinas/SSC0534/` |
| `courses/2025` | `~/Downloads/courses/2025/SSC0534/` |
| `my-courses` | `~/Downloads/my-courses/SSC0534/` |
| `.` | `~/Downloads/SSC0534/` (course folder only) |

## File Structure

```
chrome-extension/
├── manifest.json          # Extension configuration (v1.1)
├── content.js             # File detection & URL extraction (9 strategies)
├── background.js          # Service worker for downloads & path organization
├── popup.html             # Download interface
├── popup.css              # Popup styling
├── popup.js               # Popup logic & settings handler
├── settings.html          # Settings page UI
├── settings.css           # Settings styling
├── settings.js            # Settings logic & persistence
├── icon.png               # Extension icon
└── README.md              # Extension documentation
```

## How Files Are Extracted

### 9-Strategy URL Extraction System

The extension uses multiple strategies to reliably find download URLs:

1. **Direct Redirect**: If fetch follows redirect to pluginfile, use that URL
2. **Full URL Search**: Find `https://edisciplinas.usp.br/pluginfile.php/...` in HTML
3. **Relative Path**: Convert relative `/pluginfile.php/...` to absolute URL
4. **Meta Refresh**: Extract URL from `<meta http-equiv="refresh">`
5. **JavaScript Redirect**: Find URL in `location.href = "..."`
6. **Data Attributes**: Search for `data-href`, `data-src`, `data-url`
7. **Download Links**: Find `<a>` tags with pluginfile URLs
8. **File Extensions**: Look for direct links to `.pdf`, `.docx`, etc.
9. **Generic Search**: Last resort - find any pluginfile URL

### File Extension Preservation

- Extracts extension from final download URL
- Appends extension to filename: `Aula1` + `.pdf` = `Aula1.pdf`
- Normalizes to lowercase: `.PDF` → `.pdf`
- Handles URLs with query parameters: `file.pdf?token=xyz` → `pdf`
- Fallback if no extension found: uses filename as-is

## Troubleshooting

### Detailed Debugging (v1.2.0+)

The extension now includes comprehensive console logging to help diagnose issues:

1. **Enable Developer Tools**: Press `F12` on your e-Disciplinas course page
2. **Go to Console Tab**: Look for messages starting with `[e-Disciplinas]`
3. **Check Service Worker**: Go to `chrome://extensions/` → click "Service worker" link for backend logs
4. **Review Error Messages**: Look for `Strategy N matched` to see which extraction method worked
5. **See Full Guide**: Check `DEBUGGING.md` for detailed troubleshooting steps

**Key log indicators:**
- ✓ Download initiated: File successfully processed
- ⚠ Warning: Fetch or extraction issue (may still work)
- ✗ Strategy N matched: Shows which URL extraction method succeeded
- ✗ No extraction strategy matched: No file URL found in resource page

### Files Not Downloading

**Problem**: "Initiated 0 file download(s)"
- Make sure you're on an e-Disciplinas course page (https://edisciplinas.usp.br/course/view.php)
- Verify course has files visible
- Check browser download permissions are enabled

**Problem**: Files download but without extensions
- Extension has been updated to v1.1.1
- Reload at `chrome://extensions/` (click 🔄)
- Try downloading again

**Problem**: Some files missing
- Large course pages may take time to process
- Check Downloads folder for successfully downloaded files
- Look for error messages in popup status

### Configuration Issues

**Problem**: Settings not saving
- Reload extension at `chrome://extensions/`
- Verify Chrome storage is enabled (Settings → Privacy)
- Try resetting to defaults in settings page

**Problem**: Course code not detected
- Some page layouts may not expose course code
- Can manually disable "Create course subfolder" in settings
- Files will organize by custom path only

### Permission Issues

- Extension requires `downloads` permission to save files
- Grant permission when prompted by Chrome
- Check extension permissions at `chrome://extensions/`
- Verify host permission for `https://edisciplinas.usp.br/*`

## Permissions Explained

| Permission | Purpose |
|---|---|
| `activeTab` | Access current active browser tab |
| `scripting` | Inject content scripts to detect files |
| `downloads` | Programmatically trigger file downloads |
| `storage` | Save user settings persistently |
| `host_permissions` | Limited to `https://edisciplinas.usp.br/*` |

## Development

### Local Testing

1. Make changes in `chrome-extension/` folder
2. Go to `chrome://extensions/`
3. Click reload button (🔄) on extension
4. Changes take effect immediately

### File Organization in Development

- **Extension Code**: `chrome-extension/` folder
- **Documentation**: `Documentation/` folder
- **Version Control**: Git repository with full history
- **Gitignore**: Excludes `.DS_Store`, IDE files, environment variables

### Architecture Overview

```
┌─ Chrome Page
│   ├─ Detects: aalink stretched-link resources
│   └─ Extracts: course code from page
│
├─ content.js (Runs on page)
│   ├─ Find resources
│   ├─ Fetch resource pages
│   ├─ Extract URLs (9 strategies)
│   ├─ Extract extensions
│   └─ Send to background worker
│
└─ background.js (Service Worker)
    ├─ Receives download request
    ├─ Load user settings
    ├─ Build path: [custom-path]/[course-code]/[filename.ext]
    └─ Trigger Chrome download API
```

## Known Limitations

- Only works with e-Disciplinas (edisciplinas.usp.br)
- Requires Chrome/Chromium-based browsers (Edge, Brave, etc.)
- File URLs must be accessible with stored cookies
- Does not work offline
- Session-protected files may require re-authentication
- Rate limiting on bulk downloads (100+ files)

## Version History

- **v1.2.0** (Current) - Enhanced error handling, improved debugging, 15s timeout, better user feedback
- **v1.1.1** - File extension preservation fix
- **v1.1** - Download organization system with persistent settings
- **v1.0** - Initial release with basic file detection

## Future Improvements

- [ ] Download progress tracking
- [ ] Selective file downloading (choose which files to download)
- [ ] More sophisticated folder organization by topic/week
- [ ] Download scheduling and automation
- [ ] File filtering by type or date
- [ ] Integration with cloud storage
- [ ] Dark mode for settings page

## License

This extension is provided as-is for educational purposes. Feel free to fork, modify, and use for your own learning.

## Contributing

Contributions are welcome! Areas for improvement:
- Bug fixes and stability improvements
- UI/UX enhancements
- Additional file type support
- Documentation improvements
- Performance optimizations

## Support

For issues, feature requests, or questions:
1. Check existing issues in the repository
2. Review troubleshooting section above
3. Check `/Documentation` folder for detailed guides
4. Create a new issue with detailed description

## Quick Links

- 📖 **Setup Guide**: See `Documentation/CHROME_EXTENSION_SETUP.md`
- 🐛 **Debugging Guide**: See `DEBUGGING.md` for console log analysis
- ⚡ **Quick Start**: See `Documentation/QUICK_START_v1.1.md`
- 🔧 **Technical Details**: See `Documentation/IMPLEMENTATION_OVERVIEW_v1.1.md`

---

**Version**: 1.2.0
**Last Updated**: January 2025
**Status**: ✅ Production Ready

Made with ❤️ for USP students
