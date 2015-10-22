#Firelog - Realtime log viewer

Firelog is a tool that displays your Firebase log in the chrome devtools.
It's very useful especialy when you want to get live logs displayed in your chrome devtool
without connecting your device. 

# Firelog Chrome Plugin
## Install

To use Firelog as a chrome plugin during development:

 1. Go to settings in your browser
 2. Navigate to Extensions
 3. Check Developer mode
 4. Click the Load unpacked extension... button
 5. Select the chrome-extension directory
 6. Open your dev tools
 7. You should now have a firelog tab

The production version of firelog is also available in the Chrome Store (I must add the link) 

# Features 

- Deported log
- Live logs
- Keyboard shortcut to cleanup the logs (Press Cmd + K)
- I would gladly appreciate any Pull request to add more features. 

# How to use

- Setup one of the integration below.
- Open the developer console and click on firelog
- Enter the URL and press on return

# Integration 

## With XCLogger
  That's how we use it at jogabo in dev and staging

####1. Install XCLogger into your project
 
####2. Create the firelog class
  ```
  class FirelogDestination:
  
  XCGBaseLogDestination { 
    override func output(logDetails: XCGLogDetails, text: String) {
      var data = [String: NSObject]()
      data["logLevel"] = logDetails.logLevel.description
      data["date"] = NSNumber(double: round(logDetails.date.timeIntervalSince1970 * 1000))  
      data["logMessage"] = logDetails.logMessage
      data["functionName"] = logDetails.functionName
      data["fileName"] = logDetails.fileName
      data["lineNumber"] = logDetails.lineNumber
      db.logs["user-id-here"].childByAutoId().set(data)
    }
  }
  
  ```
  
####3. Add a Firelog destination to your logger
  ```
  class CrashlyticsLogDestination: XCGBaseLogDestination  {
    override func output(logDetails: XCGLogDetails, text: String) {
      let args: [CVarArgType] = [text]
      withVaList(args) { (argp: CVaListPointer) -> Void in 
        CLSLogv("%@", argp)
      }
    }
  }
  
  // MARK: globals
  log.addLogDestination(FirelogDestination(owner: log, identifier: "Firelog"))
  ```
  
#### You are more than welcome to send a Pull Request.
