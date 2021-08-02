# WSL

- running in WSL out of the box has certificate issues because of the way certificates are created and managed on windows
- to get around certificate invalid errors, you need to create a custom certificate using a script like [this](https://github.com/BorisWilhelms/create-dotnet-devcert/blob/main/create-dotnet-devcert.sh)
