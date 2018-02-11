# How to start in 60 seconds

1. Install `dotnet` template with `dotnet new -i Saturn.Template`
2. Create new folder and move into it - `mkdir SaturnSample && cd SaturnSample`
3. Create new Saturn application - `dotnet new saturn -lang F#`
3. If you're on Linux/MacOS you need to run `chmod u+x build.sh` (see: [https://github.com/SaturnFramework/Saturn/issues/15](https://github.com/SaturnFramework/Saturn/issues/15))
4. Run build process to ensure everything was scaffolded correctly and restore dependencies - `build.cmd / build.sh`
5. Go into subdirectory with server application - `cd src/SaturnSample`
6. Create new controller with `dotnet saturn gen Book Books id:string title:string author:string`
7. Run migrations that will create database and Books table (as for now, generator is using only SQLite DB) - `dotnet saturn migration`
8. Open folder in favourite editor (VSCode) and insert suggested line (`forward "/books" Books.Controller.resource`) into `browserRouter` in `Router.fs` file
9. Start application by running `build.cmd Run` from the root of solution. This will start application in watch mode (automatic recompilation on changes) and open browser on [http://localhost:8085](http://localhost:8085) which should display index page.
10. Go to [http://localhost:8085/books](http://localhost:8085/books) to see generated view. All buttons should be working, you can add new entries, remove or edit old ones.