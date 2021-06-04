# Make Me an Admin

Yet another script/workflow to provide temporary admin rights to standard users.

## Background

> "Sherman, set the wayback machine for November 25, 2013."<br/>
> "Why are we going there, Mr. Peabody?"

In 2013, I attended a presentation at the _Jamf Nation User Conference_ (JNUC) by [Andrina Kelly](https://www.linkedin.com/in/andrinakelly/) entitled, _"[Getting Users to Do Your Job (Without Them Knowing It](https://www.youtube.com/watch?v=AzlWdrRc1rY))"_.

It was, and remains, one of my favorite JNUC presentations. Andrina presented a series of requests she and her team would get from end-users, how she automated them using Self Service.

One of them included a common situation: all users have standard access but sometimes need elevated privileges. So that neither she nor her team would need to spend their days going around and typing in admin credentials for single use cases, she created the first version of this workflow. Now an end-user could go into Self Service and press a button granting them admin rights for a limited time.

I've been using versions of that workflow ever since. This is the script we are currently deploying with our customers.

## Usage

Add the script to your Jamf Pro server and label the parameters along the lines of:
4. Time (in minutes) for admin rights
5. Ask for a reason (y/n)
6. API Basic Authentication 'Token'
7. Upload log to Jamf (y/n)
8. Remove computer from static group after use?

Then create a policy as usual that runs the script and fill in the parameters as you like.

## Parameters

```
## $4 = Time (in minutes) for admin rights
TIMEMIN=$( [ $4 ] && echo $4 || echo "5" ) ## defaults to 5 minutes if not specified
TIMESEC=$(( ${TIMEMIN} * 60 ))
```
In the version provided by [Jamf](https://github.com/jamf/MakeMeAnAdmin), there are three places where the time need to be changed. One in minutes, one in seconds, and one in a string format. This version allows for one single parameter which is then calculated/converted as needed throughout the script.

```
## $5 = Ask for reason (y/n)
## If true, the user will be prompted with an AppleScript dialog why they need admin rights and the reason
## will be echoed out to the policy log.
```
If a 'y' is entered, then the user will be asked to, "Please state briefly why you need admin rights." and must supply an answer to proceed.

```
## $6 = API user hash*
## 	*hash = base64 encoded string of 'user:password' for an API user for the next two options
##		Note: Yes, I know this isn't a 'hash' but the word is more concise
## 	If provided, this string will be used in an API call to file upload the logs at the end
```
The next two options (uploading logs and removing the computer) require a Jamf Pro user with the privileges as specified below. The script uses the "Authorization: Basic" header so this parameter takes the "user:password" encoded as BASE64.

```
## $7 = Upload logs to Jamf (y/n)
## If yes, the system logs for the duration of elevated rights will be attached to the computer record in Jamf
## Note: The API user 'hash' above must be provided above and that user must have the following permissions:
##		Computers - Create
##		File Uploads - Create Read Update
```

```
## $8 = Remove computer from group after granting access?
## If this parameter exists, it must be the name of a static computer group.
## Note: The API user 'hash' above must be provided above and that user must have the following permissions:
##		Computers - Read
##		Static Computer Groups - Read Update
## After the user is promoted, the computer will be removed from the static group preventing multiple uses
```
## Future Upgrades

__TBD__

## Warnings and Disclaimers

This script is provided as-is. While we do our best to make sure it will work as described and without harmful side-effects, there are no guarantees. Use at your own risk.
