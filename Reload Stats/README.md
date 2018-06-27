# Reload Stats
## What is it?
Reload Stats is a bundle of small script utilities that allows you to track duration of user-defined script blocks.  
As a main feature, it creates a table with start, end, and duration values for your different script sections. It also provides feedback during the script load, by tracing some values.


## API
### `SetupLog`
Creates the table structure necessary to log all reload stats.

### `StartLog(string)`
Logs the starting time for the code block specified in the argument `string`.

### `StopLog(string)`
Logs the time for the finish of the block specified in the argument `string`. For this to function well, `string` should match what was used on `StartLog`. Using a variable for this is highly recommended, see usage below.

### `CleanupLog`
Removes any temporary tables and variables created by this project. Should be called at the very end of the load script.


## Fields generated
| Field                 | Format      | Example             |
| --------------------- | ----------- | ------------------- |
| ReloadStats.Stage     | String      | Calendar            |
| ReloadStats.StageNo   | Integer     | 2                   |
| ReloadStats.Start     | Timestamp*  | 25/06/2018 15:39:43 |
| ReloadStats.End       | Timestamp*  | 25/06/2018 15:39:58 |
| ReloadStats.Duration  | Interval*   | 00:00:15            |

\* Timestamps and Intervals will use the format specified by your `TimeFormat` and `TimestampFormat` variables usually found in the Main tab.

Fields are prefixed so they don't accidentally link with your data.


## How to use
Here's some simple steps on how to use it.

### Include this script
Include this script into your app **as close to the start as possible**. In Qlik Sense you will need to create a library to point at the folder where it lives or at this repository through a web file connection.  

- Folder connection
  ``` qlik
  $(Must_Include=lib://PATH_TO_YOUR_PROJECT\subReloadStats.qvs);
  ```
- Web file connection
  ``` qlik
  $(Must_Include=lib://subReloadStats);
  ```

### (Optional) Call `SetupLog`
If loaded from this repository through a Web file connection or unmodified, `SetupLog` will be called at the very end of this script.
If you want to choose when `SetupLog` is called, download this and delete the last line.

### On the first script block
It's good practice to create a variable to save the name of the stage or block of code executing.

> It is very important to choose unique names for each of the stages/code blocks.

```qlik
LET log_StageName = 'Transactions';
CALL StartLog(log_StageName);

// New code block
// ...
```

### On subsequent script blocks
Call `StopLog`, rename variable, and call `StartLog`.  
By calling `StopLog` with the same name as the previous `StartLog` it will stop logging time for that specific code block and it will log the duration with a `Trace`.  
You are free to call `StopLog` at the end of the actual code block but by calling it before the start of the new one it makes this process less intrusive as you just need to include 3 lines of code before each code block.

```qlik
//...
// Previous code block


CALL StopLog(log_StageName);
LET log_StageName = 'Calendar';
CALL StartLog(log_StageName);

// New code block
// ...
```

### On the last script block
If you are following the recommended aproach of calling `StopLog` right before the start of the new script block you will need to call it here too right before `CleanupLog`.  
It's also good practice to remove any variables you've used along the script for this purpose.

```qlik
//...
// Previous code block


CALL StopLog(log_StageName);
CALL CleanupLog;

// Deallocate variables related to Reload Stats
LET log_StageName = null();

```
