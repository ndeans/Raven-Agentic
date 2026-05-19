## Archived Prompts

### Q11: To Pierre - Antigravity Questions

Larry just made some change to the Raven_Processor and the Raven_Jobs projects to implement Operation M1. Then he ran a build and execute. The log shows errors.

Questions:
#1 - Since Operation M1 requires changes to two projects... Larry needs to be able to switch project directories without loosing context. How can I make that happen?
#2 - How can I get Larry to read the logs so he can catch the errors?
### T12: To Larry - Operation M1 Shell Script

I want you to write a bash script to run operation M1. I don't want to use Maven for this. I'd rather use the jar and call it from a shell script.

- Copy the Raven-Jobs.jar file to the new "Raven/bin" directory.
- In that same directory, write a bash script that will call the main function in the Raven-Job.jar.
*Note - this will eventually be a parameterized function call,to allow for more maintenance jobs.*
- add a job to the crontab that calls the script at 6:00 every morning.
- append what you did and how it works to the "Operation M1.md" document.