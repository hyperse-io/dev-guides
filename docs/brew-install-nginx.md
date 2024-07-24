# Mac Brew install nginx with customized `nginx` module

this tap is designed specifically for a custom build of NGINX with more module options.
https://github.com/denji/homebrew-nginx

## How do I install these formule (NGINX Modules)?

Once the tap is installed, you can install nginx-full with optional additional modules:

```shell
brew tap denji/nginx
brew install nginx-full --with-headers-more-module
```

For a list of available configuration options run:

```shell
brew options nginx-full
brew info nginx-full

```

## What about conflicts?

You are free to install this version alongside a current install of NGINX from Homebrew/homebrew if you wish. However, they cannot be linked at the same time. To switch between them use brew's built in linking system.

```shell
brew unlink nginx
brew link nginx-full
# At this point, you can use a direct command like `nginx -s reload`, but to start services, use the following `nginx-full` command
```

## start nginx

```shell
brew info nginx
brew services start nginx  (Will not start successfully)
brew services start nginx-full  (Successful start)
```
