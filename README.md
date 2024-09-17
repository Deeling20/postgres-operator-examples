# DMS Ops Console Utilities

This is a Tampermonkey script that adds utility features to the DMS (Database Migration Service) Ops Console to help support engineers. The script provides the following functionality:

   Copy Table-Mappings data: Allows the user to copy the JSON data from the "Mappings" section of the page to the clipboard.
   Convert Table-Mappings to table: Converts the JSON data from the "Mappings" section into a collapsible table format for easier viewing and analysis.
   Custom Task Settings: Compares the current task settings with the default DMS task settings and displays the differences in a modal.

The script also adds some CSS styles to enhance the appearance and usability of the added features.
Installation

## Installation
To use this script, you'll need to have the Tampermonkey browser extension installed. Once you have Tampermonkey installed, you can install the script by clicking on this link and then clicking the "Install" button in the Tampermonkey dialog.

   1. Install the Tampermonkey browser extension for [Chrome](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo) or [Firefox](https://addons.mozilla.org/en-US/firefox/addon/tampermonkey/).
   2. Navigate to the DMS_Ops_Console_Utilities.js file and click the "Raw" button.
   3. Tampermonkey will prompt you to install the script. Click "Install".
    
To use this script, you'll need to have the Tampermonkey browser extension installed. Once you have Tampermonkey installed, you can install the script by clicking on this link and then clicking the "Install" button in the Tampermonkey dialog.

## Usage

   1. Open the DMS Ops Console at https://dms-op-integ.aka.amazon.com/ops/dms_tasks/* or https://*.amazon.com/ops/dms_tasks/*.
   2. The utility buttons will appear in the bottom right corner of the page.
   3. Click the "[Console Utilities]" button to show the available options.
   4. Use the buttons to perform the desired actions.

- Copy Table-Mappings data: Click the "Copy Table-Mappings data" button to copy the JSON data from the "Mappings" section to your clipboard.
- Convert Table-Mappings to table: Click the "Convert Table-Mappings to table" button to display the JSON data in a collapsible table format enabling to easily see the transformation rules/ selection.
- Custom Task Settings: Click the "Custom Task Settings" button to compare the current task settings with the default DMS task settings and view the differences in a modal.

The script will automatically add a "Console Utilities" button to the bottom-right corner of the DMS Ops Console page. Clicking this button will toggle the display of the available utility options.
Contributions

If you have any suggestions or find any issues with the script, please feel free to create a new issue in the repository.
