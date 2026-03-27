buddy step by step first i have Dockerfile and app.js in killercoda host /root/node/ location

11:53 PM
Perfect! Let's go step by step. Your files are at /root/node/ on the KillerCoda host.

Step 1 — Verify your files
bash
ls /root/node/
You should see Dockerfile and app.js ✅

Step 2 — Get your Jenkins container name
bash
docker ps
Look for your Jenkins container and note the name (e.g. jenkins, jenkins_1, etc.)

Step 3 — Get your Jenkins Job name
Go to Jenkins UI → note the exact job name you created (e.g. nodejs-pipeline)

Step 4 — Create the workspace folder inside Jenkins
bash
docker exec <jenkins-container-name> mkdir -p /var/jenkins_home/workspace/<your-job-name>
Example:

bash
docker exec jenkins mkdir -p /var/jenkins_home/workspace/nodejs-pipeline
Step 5 — Copy your files into Jenkins workspace
bash
docker cp /root/node/. <jenkins-container-name>:/var/jenkins_home/workspace/<your-job-name>/
Example:

bash
docker cp /root/node/. jenkins:/var/jenkins_home/workspace/nodejs-pipeline
Step 6 — Verify files are inside Jenkins
bash
docker exec <jenkins-container-name> ls /var/jenkins_home/workspace/<your-job-name>/
```

Expected output:
```
Dockerfile
app.js
