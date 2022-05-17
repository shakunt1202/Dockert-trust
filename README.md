
# Dockert Content Trust(DCT)

Docker Content Trust (DCT) provides the ability to use digital signatures for data sent to and received from remote Docker registries. These signatures allow client-side or runtime verification of the integrity and publisher of specific image tags.

## Prerequisites
- Latest Docker desktop 
- Docker hub account
- Docker contrainer image
## Documentation

[Documentation](https://docs.docker.com/engine/security/trust/)


## Deployment
### Make a docker repository for your images
- Go to [Docker Hub](https://hub.docker.com/) and sign in with your docker hub account
- Click on Create repository and name it "test"
- It should look something like this.
------ screeshots

### Do Docker login in your CLI with 
```bash 
Docker login 
```
- Enter your credentials and you are ready to go.

### To Sign a docker image we have first generate a key...
To generate a key run this command in your CLI

```bash
  docker trust key generate signature
```
- This "Signature" is the name you will give to your key and it will be saved in your working directory with the .pub extension
- After running this command you will be asked to enter and repeat a passphrase.

To make a signer and add it to your repository run this command
```bash 
docker trust signer add --key signature.pub [signer's name] [your repository's name]
```
- The repository name will be something like this --> example/test
- You have to enter and repeat passphrase for root key and repository.

### Signing and Pushing images to the repository
- First let's commit our container image.
```bash
 docker commit [Image ID] [Repository Name]
```
- You can find the image ID from
```bash
docker ps
``` 
- Let's push an unsigned image for reference
```bash
 docker push [Repository Name]:unsigned-image
```
- Now sign the tag and sign the same image.
Tag the image with 
```bash
docker image tag [Image ID] [Repository Name]:signed-image
```
Sign the image with
```bash
docker trust sign [Repository Name]:signed-image
```
- You have to again enter the passphrase

For the the further imformation about the signed image run
```bash
docker trust inspect --pretty [Repository Name]:signed-image
```
Push the signed image with 
```bash
docker push [Repository Name]:signed-image

The push refers to repository [docker.io/shakunt/test]
059ff50d778b: Layer already exists
c4e64d78638e: Layer already exists
5f70bf18a086: Layer already exists
0cd0f4e90e0c: Layer already exists
e4a7f8c5002b: Layer already exists
7cc0623bd7a8: Layer already exists
f1859b30ca6b: Layer already exists
6a35d52a66fd: Layer already exists
fbd7d5451c69: Layer already exists
4fc242d58285: Layer already exists
trust-image-signed: digest: sha256:f6274d55e7ae079737180c7cb5----7387fb6a87297ef486edbc1bb16f4d0 size: 2409
```

### Setting up trust environment and pulling the signed and unsigned images

To set up the trust environment run
```bash
export DOCKER_CONTENT_TRUST=1
```
Let's pull the images
- unsigned image
```bash
docker pull [Repository Name]:unsigned-image
No valid trust data for unsigned-image
```
- Signed image
```bash
docker pull [Repository Name]:signed-image
Pull (1 of 1): shakunt/test:signed-image@sha256:f6274d55e7ae079737180c7cb5b02f386edbc1bb16f4d0
docker.io/shakunt/test@sha256:f6274d55e7ae079737180c7cb5b02f3767387fb6a87bb16f4d0: Pulling from shakunt/test
Digest: sha256:f6274d55e7ae079737180c77fb6a87297ef486edbc1bb16f4d0
Status: Image is up to date for shakunt/test@sha256:f6274d551bb16f4d0
Tagging shakunt/test@sha256:f6274d55e7ae079732f3767387fb6a87297ef486edbc1bb16f4d0 as shakunt/test:signed-image
docker.io/shakunt/test:signed-image
```
## Screenshots

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

