# Get OneDrive File
## What is it?
A function that makes it easier to get files from Office 365 OneDrive / SharePoint

## API
### `getOneDriveFile(QwcConnectionName,QwcServer,QwcToken,OneDriveFilePath)`
Get the file! 
Sets the value of vOneDriveFileUrl for later use.

## How to use
Here's some simple steps on how to use it.

Easy to use once you have it set up, but you might want to do something like
```
call GetOneDriveFile('$(vQwcConnectionName)','$(vQwcServer)','$(vQwcToken)','/sites/Operations/Shared Documents/General/Operations Apps/Working Days Per Month.xlsx');
	trace '$(vOneDriveFileUrl)';
```
Then copy/paste the trace into a new Web file connection and use that to set up your actual file extracts.
Once done, replace the xxx in "From [xxx]" with the $(vQwcConnectionName)
And add in "URL is $(vOneDriveFileUrl)" inside the ()
Then, optionally, delete the temporary web file connection

### Example:
```
// Optional but useful config for repeating calls
let vQwcConnectionName='lib://QlikWebConnectors';
let vQwcServer='https://myQlikServer.mydomain.com:5555';
let vQwcToken='abc123xyz';

// Wants QwcConnectionName, QwcServer, QwcToken, OneDriveFilePath. Populates vOneDriveFileUrl variable
call GetOneDriveFile('$(vQwcConnectionName)','$(vQwcServer)','$(vQwcToken)','/sites/Operations/Shared Documents/General/Operations Apps/Working Days Per Month.xlsx');

trace $(vOneDriveFileUrl);

NonWorkingDays:
LOAD
    [Date],
    0 as WorkingDay
FROM [$(vQwcConnectionName)]
(URL is [$(vOneDriveFileUrl)], 
	ooxml, embedded labels, table is [Holidays]);
```

