# GitOps GitHub Container Registry
## (Docker edition)

- **Step 1: Create a new repository [https://github.com/new]**  
For this tutorial, let's name the repository “dkr\_ghcr\_mallet”  
You can give your repo a Description, add a README file, and choose what sort of license to give it (or skip).  
After we're done naming our repository, ensure that this repository is public.

- **Step 2: Create a new file**  
You can create any program with files of your choice, but we're working with Docker, so let's create one named:  
 ```Dockerfile```  
  
My Dockerfile for Mallet is:<br>
```
# Use ubuntu as base image
FROM ubuntu:latest

# Set environment variable for Mallet
ENV MALLET_HOME /usr/local/mallet
ENV PATH $MALLET_HOME/bin:$PATH

# Install necessary packages
RUN apt-get update && \
    apt-get install -y openjdk-11-jre wget unzip tar nano gpg curl ca-certificates curl gnupg -y && \
    sudo install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/l inux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    sudo chmod a+r /etc/apt/keyrings/docker.gpg && \
    source ~/.bashrc && \
#     install -m 0755 -d /etc/apt/keyrings && \
#     curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
#     sudo chmod a+r /etc/apt/keyrings/docker.gpg && \ 
    rm -rf /var/lib/apt/lists/*

# lines 33-39 commented out...uncomment if you don't want Docker installed inside your Mallet container
# RUN echo \
#     "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
#     "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
#     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# RUN apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Download Mallet
WORKDIR /usr/local/
RUN wget -O mallet-202108_cds-custom.tar.gz "https://cody.digitalscholarship.brown.edu/mallet-202108_cds-custom.tar.gz" && \
    tar -zxf mallet-202108_cds-custom.tar.gz && \
    mv mallet-202108_cds-custom mallet && \
    rm mallet-202108_cds-custom.tar.gz

# Set the working directory
WORKDIR /usr/local/mallet

# Set the default command to run Mallet
# CMD ["bin/mallet"]
```  
  
Save, Commit Changes  
  
- **Step 3: To access GitHub container registry you need to create Personal Access Token (PAT) on GitHub:**  
From the Github website “Settings > Developer Settings > Personal access tokens” and create token with select and enable permissions related to “packages”. 
The current permalink for this is: [https://github.com/settings/tokens/new]:  
    - Select the read:packages scope to download container images and read their metadata.  
    - Select the write:packages scope to download and upload container images and read and write their metadata.  
    - Select the delete:packages scope to delete container images.  
    - Create your PAT, and copy the string Github created.

- **Step 4: Add your token to your Terminal Environment, Authenticate to the Github Container Registry**  
Open Terminal, and enter:
```export CR_PAT=token you copied goes here```  
Then authenticate to the Github Container Registry  
```echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin```  
_be sure you replace USERNAME above with your Github username_

- **Step 5: Tag