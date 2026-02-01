## Hi there 👋 

Check out https://mcphylus12.github.io

Im a full stack dev with most of my experience in C# but enjoy picking up whatever is needed to get the job done.
I have a particular interest in clean design and architecture and how to keep an application high quality over a long lifetime while being maintained by more than 1 person.


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
- HttpMocker. My take on mocking Http calls by converting MVC controller like syntax directly into a HttpMessageHandler.
    -  https://github.com/Mcphylus12/HttpMocker
- DataGenerator. A stub data generator that is resursive and supports type and property specific rules.
    -  https://github.com/Mcphylus12/DataGenerator
- Config Api. A standalone web service that can be deployed and used to manage the configuration of other products and services in an ecosystem.
   - Built to faciliate shipping a SAAS style microservice system to an on prem target as it allows the customer admins a way to modify all config in the application through a single place
   - Supports config inheritance and reuse through dot sepeated keys. View the project README for an example
   - https://github.com/Mcphylus12/ConfigAPI
- All in one telemetry object
   - A single IDisposable that gets you alot of telemetry quickly
   - https://gist.github.com/Mcphylus12/e50985410624e4c2e3f431ea271ef474
- Inline HTTP mocker for frontends
   - https://github.com/Mcphylus12/InlineMocker
   - A single JS file that can be dropped in and provides the ability to intercept calls from your app and return mocked responses.
   - Built in UI to manage the mocked responses
   - Supports importing and exporting mocked responses
