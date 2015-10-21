# travis-deploy-sonatype
Support for travis-ci.org to deploy to sonatype

## Instructions for integration with travis/sonatype
Instructions start assuming that you are able to build & deploy by yourself.

### Create a new gpg key for sonatype pushing and remember its hash
```
> gpg2 --gen-key
> gpg2 --list-secret-keys
sec   2048R/0F8B00C7 2015-10-20
uid                  SCR Travis Deployment (Sheridan Rawlins Travis Deployment) <sheridan.rawlins+travis@yahoo.com>
ssb   2048R/6A238AB5 2015-10-20
# In this case, the hash is 0F8B00C7
```

### Create your settings.xml
```
> mkdir travis
> cd travis
> curl -O https://raw.githubusercontent.com/scr/travis-deploy-sonatype/master/travis/settings.xml
```

### Encrypt your variables
See http://docs.travis-ci.com/user/encryption-keys/

```
> sudo gem install travis # if you haven't already
>  travis encrypt SONATYPE_USER=your-sonatype-username --add
>  travis encrypt SONATYPE_PASSWD=your-sonatype-passwd --add
>  travis encrypt GPG_USER=the-hash-from-above --add
>  travis encrypt GPG_PASSWD=the-password-from-above --add
```

### Export an encrypted version of your gpg key
See http://docs.travis-ci.com/user/encrypting-files/

```
> gpg2 -a --export-secret-keys the-hash-from-above > somename.asc
> travis encrypt-file somename.asc

# You'll see output like the following - In this example "0a6446eb3ae3" is the ENC_HASH you will use later
    openssl aes-256-cbc -K $encrypted_0a6446eb3ae3_key -iv $encrypted_0a6446eb3ae3_key -in super_secret.txt.enc -out super_secret.txt -d

```

### Update your travis with the following settings:
```
after_success:
- bash scripts/deploy.sh the-ENC_HASH-from-above travis/somename-from-above.asc travis/settings.xml
sudo: true
```
