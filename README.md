# BBB-Docker-PostEvents
This repo modifies bbb-docker setup to support post_events_analytics_callback.rb scripts based on [this article](https://jffederico.medium.com/bigbluebutton-activity-completion-in-moodle-b3269adf4c34) Thanks to Jesus Federico.  
Personally I had to make this modifications so I could use BigBlueButton's activity completion in my LMS platform (Moodle v5.1+) using dockerized bbb setup. 
This repo is build after I tested it all and it's stable, I never tried to run them on another server because I didn't have any, if you tried it and it faliled, open new issue and send logs so we could fix it ;) 

# How it works
The file bbb-web.properties must be also readable to "recordings" container after being processed by "bbb-web" container because of the nature of this dockerized setup. 
By modifying and updating the "bbb-web" container, you can update your bbb-web.properties file the way you want and build the container so the modifications become permanent. 
Also some postevents scripts needs some requirements that must be installed, this method also updates the "recordings" container which makes requirements installations permanent. 
For the final step, we modify the docker-compose.yml file so the "recordings" image have access to final bbb-web.properties

# Setup
## Step 1: Clone using git:
For best experience, it's recommended to clone this repo inside bbb docker root folder:
```
cd PATH_TO_YOUR_BBB_ROOT
```
Clone:
```
git clone https://github.com/Ashaxer/BBB-Docker-PostEvents.git .
cd BBB-Docker-PostEvents
```

## Step 2: Modify bbb-web container changes:
Copy your bbb-web.properties from mod/bbb-web folder from your dockerized bbb setup to mod-overrides/bbb-web/
```
cp PATH_TO_YOUR_bbb-web.properties mod-overrides/bbb-web/bbb-web.properties
```
For porpuse of working analytics_callback, add ```defaultKeepEvents={{ .Env.ENABLE_KEEP_EVENTS | default "true" }}``` at the end of the file
```
nano mod-overrides/bbb-web/bbb-web.properties
```

## Step 3: Build to make the changes permanent:
*Before applyhing any changes to your container, make sure what you are doing, I replaced my container and "it works on my machine". feel free to create issues*

If you want to create a new container:
```
docker build -t bbb-docker-web:custom .
```

If you want to replace your current container:
```
docker build -t alangecker/bbb-docker-web:v3.0.4 .
```

## Step 4: Modify recordings container changes:
For porpuse of working analytics_callback, I added some requirement installations into Dockerfile, you can edit it and build it whenever you want.
```
nano mod-overrides/recordings/Dockerfile
```

## Step 5: ## Step 3: Build to make the changes permanent:
*Before applyhing any changes to your container, make sure what you are doing, I replaced my container and "it works on my machine". feel free to create issues*

If you want to create a new container:
```
docker build -t bbb-docker-recordings:custom .
```

If you want to replace your current container:
```
docker build -t alangecker/bbb-docker-recordings:v3.0.4 .
```

## Step 6: Update docker-compose and scripts file 
The updated bbb-web.properties has to be in ```/usr/share/bbb-web/WEB-INF/classes/bigbluebutton.properties``` path, that's what my postevent script needs to read bbb properties from. and we also need to share this path to scripts folder so our scripts are mounted into recordings container. so we just add this line into recordings part of docker-compose.yml (and docker-compose.tmpl.yml if you don't want to lose your changes after using /scripts/generate-compose) which must look like this:
```
  recordings:
    ...
    volumes:
      ...
      - ./custom-scripts/post_events:/root/bbb/scripts/post_events:ro
      - ./data/bigbluebutton/bbb-web.properties:/usr/share/bbb-web/WEB-INF/classes/bigbluebutton.properties:ro
```

Make the necessary changes to scripts inside custom-scripts folder; add/remove/edit your scripts the way you want  
The current post_events_analytics_callback.rb is modified which works with Dockerized BBB 

## Step 7: Apply changes to docker
To apply the changes inside docker-compose.yml file, you must shut down all docker images and run them again, docker compose restart wont work! 
Go to your bbb folder and run this:
```docker compose down && docker compose up -d```
or
```docker-compose down && docker-compose up -d```
