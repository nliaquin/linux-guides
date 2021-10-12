# Configuring Git
This is just going to be a quick little reference guide to configuring Git quickly.

### Table of Contents
 - [Configuring Credentials](#configuring-credentials)
 - [Git Password](#git-password)
 - [Storing Credentials](#storing-credentials)
 
### Configuring Credentials
You need to first set your username:
> git config --global user.name **your name**

Then configure your email, and make sure it's the same email as your github/gitlab/git server account:
> git config --global user.email **your email**

You can verify these credentials are correct by running the following command:
> git config --list

### Git Password
When you're prompted to enter your password when pushing changes to the server, you'll want to use a key instead of your password. Your password should be for logging in, but on github and gitlab, there are options to use session keys instead. You can limit how long session keys are active and even revoke session keys from your login.

### Storing Credentials
In order to not have to enter your credentials every time you push or pull on any project, enter the following command:
> git config --global credential.helper store

Be warned, on the current device you're on, this will apply to all repositories. If you use a different account for different projects, this will be a problem.

If you want to just store credentials for the project in the directory you are currently in, use:
> git config credential.helper store

This way it's not global. It's still a little unsafe to do this, and so you could choose to store via cache instead of a file like the other two methods use:
> git config --global credential.helper cache

It's not permanent, and it's only going to be for the current session until you log out. It's still safer than storing to a file, but if you're working on a device that is exclusively yours and is password protected, any of the above options should be fine.
