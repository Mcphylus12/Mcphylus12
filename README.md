## Hi there 👋 

Im a full stack dev with most of my experience in C# but enjoy picking up whatever is needed to get the job done.
I have a particular interest in clean design and architecture and how to keep an application high quality over a long lifetime while being maintained by more than 1 person.
Gamer outside of works an im usually up for anything team based regardless of thats involved.


### Current projects
Im slowly working through project ideas
- AutoFront (Thank ChatGPT for the name). The project is a dynamic fontend that can be used to present business data and trigger business functions. The aim is to allow developers who dont want to touch frontend tech to still be able to put together something quick for things like back office applications.
    - Written in SolidJS with javascript
    - Contains an open api spec that a dev can bui;d a backend server for the frontend to automatically discover what pages to display and what business object and actions are available.
    - https://github.com/Mcphylus12/Autofront
- Monobuilder. A tool to support easy dependency and build management for monorepos.
    - feed it a config of your dependencies (it will also support discovery through reading things like csproj files)
    - When you want to build give it a list of changed folders and files EG `git diff --name-only` (Also can feed in changes from a file)
    - And it will throw out the build scripts for each project that needs building
    - https://github.com/Mcphylus12/MonoBuilder
- TestManager. A tool for managing file based tests
    - Define tests in a json format against files
    - run against a directory to execute/manage tests
    - Bulk cli mode takes a glob pattern and executes all tests in the json files that match the pattern
    - Run in a web mode to get a web ui to manage tests
    - https://github.com/Mcphylus12/TestManager
- Messenger. Basic Mediatr inspired dispatcher that also allows you to send it in one service and handle it in another.
    - Built to make moving from monolith to microservice and back again an easier exercise
    - https://github.com/Mcphylus12/Messenger
