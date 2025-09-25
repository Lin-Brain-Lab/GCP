# GCP
A short instruction of how to do Neuroimaging using Google cloud (based on FSL)
## Shell script commands
To start with, there are some handy commands you should keep in mind, such as:  
1. **[gsutil](https://cloud.google.com/storage/docs/gsutil)** (what you do to call/edit your bucket)
   
     ```sh
     gsutil -m -cp YOUR_CURRENTFILES gs://YOUR_BUCKETNAME
     ```
3. **[gcloud auth login](https://cloud.google.com/sdk/gcloud/reference/auth/login)** (what you do to get access to other proj/bucket)

     ```sh
     gcloud auth login
     ```
## Install FSL on Vertex AI workbench
1. Create a Google cloud account 
2. Go to google cloud console, pick [Vertex AI](https://cloud.google.com/vertex-ai/docs/workbench/introduction) on the menu and create a workbench
3. Choose machine and storage (there are some limitations here, should check on your quotas)
4. Start the VM and open up terminal
5. [Install FSL](https://fsl.fmrib.ox.ac.uk/fsl/docs/#/install/linux)

     ```sh
     curl -Ls https://fsl.fmrib.ox.ac.uk/fsldownloads/fslconda/releases/getfsl.sh | sh -s
     ```
7. Open up **another** terminal

<br /> You are all set!

## Run FSL on Vertex AI workbench
* If you are running 1st level analysis for multiple subjects, I would suggest you to use **parallel** to boost up the speed

     ```sh
     sudo apt-get install parallel   # Ubuntu/Debian
     ```
  example code to add on your script so it can run with parallel:

     ```sh
     cat "${DATA_PATH}/subject_list.txt" | parallel -j "$PARALELJOBSCOUNT" YOURMAINCODE {}
     ```
* gsfuse may not be a good idea as there might be some restrictions when running fsl and leads to error
* Better way to do is to mount your storage and read files from storage, work on your VM and upload back to storage

## Install FSL on Compute engine
* This is an alternative way to run fsl on google cloud, but you might need to adjust your script so the route to fsl can be set correctly
1. Establish your VM by choosing Compute Engine >> VM instance
2. Choose the machine and storage
3. [Mount Disk](.mount-disk/README.md) (the root storage does not have enough space for instaling FSL)

5. [Install FSL](https://fsl.fmrib.ox.ac.uk/fsl/docs/#/install/linux)

     ```sh
     curl -Ls https://fsl.fmrib.ox.ac.uk/fsldownloads/fslconda/releases/getfsl.sh | sh -s
     ```
   (if this doesn't work, go and download getfsl.sh and edit the file)

<br /> Complete!
* Debugging:
   *  fsl:command not found
   <br /> **Edit bashrc**
```sh
FSLDIR=/MOUNT_DIR/fsl
. ${FSLDIR}/etc/fslconf/fsl.sh
PATH=${FSLDIR}/bin:${PATH}
export FSLDIR PATH

```
## Mount Disk
* Add disk to VM
1. click on the VM and edit
2. add disk and choose the volume
3. check if disk exist
      ```sh
      lsblk
      ```
4. Check if mounted(usually not properly mounted in the first time)
      ```sh
      df -h
      ```
6. You have to format the disk:
       ```sh
       sudo mkfs.FILE_SYSTEM_TYPE -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/DEVICE_NAME
       ```
7. Create directory
8. Mount
       ```sh
       sudo mount -o discard,defaults /dev/DEVICE_NAME /MOUNT_DIR
       ```
* Fusing cloud storage
1. Make sure your google cloud permits access from google VM
    <br />Stop your vm--> Edit VM --> see Access rights scope --> Set access rights for each API seperately
2. Install gcsfuse
       ```sh
       sudo apt-get update
       sudo apt-get install gcsfuse
       ```
3. Make a folder for fusing point and grant access
       ```sh
       mkdir FOLDER
       chmod 777 FOLDER
       ```
4.  Start fusing
       ```sh
       gcsfuse BUCKET_NAME FOLDER
       ```
   * if you have several subfolders you can do this: gcsfuse
      ```sh
      --implicit-dirs BUCKET_NAME FOLDER
      ```
5. Check if you have access
      ```sh
      cd FOLDER
      ls -l
      ```

## Other usefull stuff  
* [Using local terminal to run your google cloud](https://www.youtube.com/watch?v=hP9B3xXP1Ts)
* [Mount automatically](https://www.youtube.com/watch?v=pDC3WrNhpZQ)

## References
[Google Cloud](https://cloud.google.com/compute/docs/disks/format-mount-disk-linux)
<br />[Youtube Tutorials](https://www.youtube.com/watch?v=pDC3WrNhpZQ)
<br />[Youtube Tutorials](https://www.youtube.com/watch?v=AASvXXbgswg)






