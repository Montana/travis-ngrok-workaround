# Using ngrok to live debug your Travis builds

Last night I did some digging and found out `tmate` will be deprecated. So let's now debug your Travis CI builds using ngrok, this method will for now let you live-debug your builds as I said `tmate` will no longer be maintaining their project. Add the following to your `.travis.yml`: 

```yaml
env:
  NGROK_TOKEN=...

addons:
  apt:
    packages:
      - ssh 

after_script:
  - ./run_ngrok.sh

```
Here's the bash script that will allow `ngrok` to run, and remember to make it executable so it can run in Travis.

```bash
#!/bin/bash

curl -o ngrok.zip https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip || exit $?
unzip ngrok.zip || exit $?
chmod +x ngrok

echo "ssh-rsa $YOUR_SSH_PUBLIC_KEY_GOES_HERE" > ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
echo "Starting sshd..."
sudo systemctl start sshd || exit $?

echo "Starting ngrok..."
./ngrok tcp 22 --authtoken=$NGROK_TOKEN --log=stdout --log-level=debug
echo "ngrok exited $?" 
```
Then you'll want to wait for a message saying "Starting ngrok", and it will print a URL out with a Dashboard, and then give you SSH credentials. 

