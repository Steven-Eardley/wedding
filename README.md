# Wedding Website

## Deployment

This is managed using git hooks. You'll need the server in your ```.ssh/config``` file,
and the remote:

    git remote add production user@server:wedding.git

You can only deploy the master branch. To do so, run the following command:

    git push production master

i.e. you are pushing master to the production remote. This will run the hooks, and
restart ```nginx``` for you.

## Server configuration

Requires a bare git repo on the host, called ```wedding.git```. This is created
with ```git init --bare wedding.git``` in the home directory.

Create the working tree directory where nginx will serve the files from and fix the
permissions:

    sudo mkdir /var/www/wedding
    sudo chown www-data:www-data /var/www/wedding
    sudo chmod -R 775 /var/www
    sudo usermod -a -G www-data user

The post receive hook then needs to be created on the host, by copying
```deploy/hooks/post-receive``` to ```~/wedding.git/hooks/post-receive``` and
```chmod +x hooks/post-receive``` on the host. This will allow the script to run on
deploy and copy the code to the correct work tree.

Afterwards, replace the copied hook with a symlink to the checked out hook (so you can update the hooks):

    ln -sf /var/www/wedding/deploy/hooks/post-receive hooks/post-receive
    chmod +x hooks/post-receive

## SSL Certificates

The nginx config expects an SSL certificate - this should be created via LetsEncrypt / certbot.