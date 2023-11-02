# SkyAuth-Python-Example
SkyAuth Python example for the https://skyproject.cc authentication system.

## **Bugs**

If the default example not added to your software isn't functioning how it should, please report a bug here https://skyproject.cc/app/?page=support

## **How to compile?**

You can either use Pyinstaller or Nuitka.

Links:
- Nutika: https://nuitka.net/
- Pyinstaller: https://pyinstaller.org/

Pyinstaller:
- Basic command: `pyinstaller --onefile main.py`

Nutika:
- Basic command: `python -m nuitka --follow-imports --onefile main.py`

## **`SkyAuthApp` instance definition**

Visit https://skyproject.cc/app/ and select your application, then click on the **Python** tab

It'll provide you with the code which you should replace with in the `main.py` file.

```PY
SkyAuthApp = api(
    name = "example", #App name (Manage Applications --> Application name)
    ownerid = "JjPMBVlIOd", #Owner ID (Account-Settings --> OwnerID)
    secret = "db40d586f4b189e04e5c18c3c94b7e72221be3f6551995adc05236948d1762bc", #App secret(Manage Applications --> App credentials code)
    version = "1.0",
    hash_to_check = getchecksum()
)
```

## **Initialize application**

You don't need to add any code to initalize. SkyAuth will initalize when the instance definition is made.

## **Display application information**

```py
SkyAuthApp.fetchStats()
print(f"""
App data:
Number of users: {SkyAuthApp.app_data.numUsers}
Number of online users: {SkyAuthApp.app_data.onlineUsers}
Number of keys: {SkyAuthApp.app_data.numKeys}
Application Version: {SkyAuthApp.app_data.app_ver}
Customer panel link: {SkyAuthApp.app_data.customer_panel}
""")
```

## **Check session validation**

Use this to see if the user is logged in or not.

```py
print(f"Current Session Validation Status: {SkyAuthApp.check()}")
```

## **Check blacklist status**

Check if HWID or IP Address is blacklisted. You can add this if you want, just to make sure nobody can open your program for less than a second if they're blacklisted. Though, if you don't mind a blacklisted user having the program for a few seconds until they try to login and register, and you care about having the quickest program for your users, you shouldn't use this function then. If a blacklisted user tries to login/register, the SkyAuth server will check if they're blacklisted and deny entry if so. So the check blacklist function is just auxiliary function that's optional.

```py
if SkyAuthApp.checkblacklist():
    print("You've been blacklisted from our application.")
    os._exit(1)
```

## **Login with username/password**

```py
user = input('Provide username: ')
password = input('Provide password: ')
SkyAuthApp.login(user, password)
```

## **Register with username/password/key**

```py
user = input('Provide username: ')
password = input('Provide password: ')
license = input('Provide License: ')
SkyAuthApp.register(user, password, license)
```

## **Upgrade user username/key**

Used so the user can add extra time to their account by claiming new key.

> **Warning**
> No password is needed to upgrade account. So, unlike login, register, and license functions - you should **not** log user in after successful upgrade.

```py
user = input('Provide username: ')
license = input('Provide License: ')
SkyAuthApp.upgrade(user, license)
```

## **Login with just license key**

Users can use this function if their license key has never been used before, and if it has been used before. So if you plan to just allow users to use keys, you can remove the login and register functions from your code.

```py
key = input('Enter your license: ')
SkyAuthApp.license(key)
```

## **User Data**

Show information for current logged-in user.

```py
print("\nUser data: ")
print("Username: " + SkyAuthApp.user_data.username)
print("IP address: " + SkyAuthApp.user_data.ip)
print("Hardware-Id: " + SkyAuthApp.user_data.hwid)

subs = SkyAuthApp.user_data.subscriptions  # Get all Subscription names, expiry, and timeleft
for i in range(len(subs)):
    sub = subs[i]["subscription"]  # Subscription from every Sub
    expiry = datetime.utcfromtimestamp(int(subs[i]["expiry"])).strftime(
        '%Y-%m-%d %H:%M:%S')  # Expiry date from every Sub
    timeleft = subs[i]["timeleft"]  # Timeleft from every Sub

    print(f"[{i + 1} / {len(subs)}] | Subscription: {sub} - Expiry: {expiry} - Timeleft: {timeleft}")
print("Created at: " + datetime.utcfromtimestamp(int(SkyAuthApp.user_data.createdate)).strftime('%Y-%m-%d %H:%M:%S'))
print("Last login at: " + datetime.utcfromtimestamp(int(SkyAuthApp.user_data.lastlogin)).strftime('%Y-%m-%d %H:%M:%S'))
print("Expires at: " + datetime.utcfromtimestamp(int(SkyAuthApp.user_data.expires)).strftime('%Y-%m-%d %H:%M:%S'))
print(f"Current Session Validation Status: {SkyAuthApp.check()}")
```

## **Show list of online users**

```py
onlineUsers = SkyAuthApp.fetchOnline()
OU = ""  # KEEP THIS EMPTY FOR NOW, THIS WILL BE USED TO CREATE ONLINE USER STRING.
if onlineUsers is None:
    OU = "No online users"
else:
    for i in range(len(onlineUsers)):
        OU += onlineUsers[i]["credential"] + " "

print("\n" + OU + "\n")
```

## **Application variables**

A string that is kept on the server-side of SkyAuth. On the dashboard you can choose for each variable to be authenticated (only logged in users can access), or not authenticated (any user can access before login). These are global and static for all users, unlike User Variables which will be dicussed below this section.

```py
* Get normal variable and print it
data = SkyAuthApp.var("varName")
print(data)
```

## **User Variables**

User variables are strings kept on the server-side of SkyAuth. They are specific to users. They can be set on Dashboard in the Users tab, via SellerAPI, or via your loader using the code below. `discord` is the user variable name you fetch the user variable by. `test#0001` is the variable data you get when fetching the user variable.

```py
* Set up user variable
SkyAuthApp.setvar("varName", "varValue")
```

And here's how you fetch the user variable:

```py
* Get user variable and print it
data = SkyAuthApp.getvar("varName")
print(data)
```

## **Application Logs**

Can be used to log data. Good for anti-debug alerts and maybe error debugging. If you set Discord webhook in the app settings of the Dashboard, it will send log messages to your Discord webhook rather than store them on site. It's recommended that you set Discord webhook, as logs on site are deleted 1 month after being sent.

You can use the log function before login & after login.

```py
* Log message to the server and then to your webhook what is set on app settings
SkyAuthApp.log("Message")
```

## **Ban the user**

Ban the user and blacklist their HWID and IP Address. Good function to call upon if you use anti-debug and have detected an intrusion attempt.

Function only works after login.

```py
SkyAuthApp.ban()
```

## **Logout session**

Logout the users session and close the application. 

This only works if the user is authenticated (logged in)
```py
SkyAuthApp.logout()
```

## **Server-sided webhooks**

Send HTTP requests to URLs securely without leaking the URL in your application. You should definitely use if you want to send requests to SellerAPI from your application, otherwise if you don't use you'll be leaking your seller key to everyone. And then someone can mess up your application.

1st example is how to send request with no POST data. just a GET request to the URL. `7kR0UedlVI` is the webhook ID, `https://skyproject.cc/api/seller/?sellerkey=sellerkeyhere&type=black` is what you should put as the webhook endpoint on the dashboard. This is the part you don't want users to see. And then you have `&ip=1.1.1.1&hwid=abc` in your program code which will be added to the webhook endpoint on the SkyAuth server and then the request will be sent.

2nd example includes post data. it is form data. it is an example request to the SkyAuth API. `7kR0UedlVI` is the webhook ID, `https://skyproject.cc/api/1.2/` is the webhook endpoint.

3rd examples included post data though it's JSON. It's an example reques to Discord webhook `7kR0UedlVI` is the webhook ID, `https://discord.com/api/webhooks/...` is the webhook endpoint.

```py
* example to send normal request with no POST data
data = SkyAuthApp.webhook("7kR0UedlVI", "&ip=1.1.1.1&hwid=abc")

* example to send form data
data = SkyAuthApp.webhook("7kR0UedlVI", "", "type=init&name=test&ownerid=j9Gj0FTemM", "application/x-www-form-urlencoded")

* example to send JSON
data = SkyAuthApp.webhook("7kR0UedlVI", "", "{\"content\": \"webhook message here\",\"embeds\": null}", "application/json")
```

## **Download file**

`385624` is the file ID you get from the dashboard after adding file.

```py
* Download Files form the server to your computer using the download function in the api class
bytes = SkyAuthApp.file("385624")
f = open("example.exe", "wb")
f.write(bytes)
f.close()
```

## **Chat channels**

Allow users to communicate amongst themselves in your program.

Example from the form example on how to fetch the chat messages.

```py
* Get chat messages
messages = SkyAuthApp.chatGet("CHANNEL")

Messages = ""
for i in range(len(messages)):
Messages += datetime.utcfromtimestamp(int(messages[i]["timestamp"])).strftime('%Y-%m-%d %H:%M:%S') + " - " + messages[i]["author"] + ": " + messages[i]["message"] + "\n"

print("\n\n" + Messages)
```

Example on how to send chat message.

```py
* Send chat message
SkyAuthApp.chatSend("MESSAGE", "CHANNEL")
```
