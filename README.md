# CI emulator
CI emulator will set up a local Zuul instance as your project gating system in combination with Gerrit for code review. Example from [Quick-Start Installation and Tutorial](https://zuul-ci.org/docs/zuul/tutorials/quick-start.html) was used as a basis for this emulator and is worth checking out if you want to learn more. This emulator is entirely self-contained and may be safely run on a workstation. 

The only requirements are a network connection and the ability to run Docker containers. It is also advised to use **Ubuntu (Bionic Beaver)** since it was used for development and testing of this emulator.

You can install all the requirements for running CI emulator by running these commands:
```
sudo apt-get install docker-compose docker.io git python3-pip
sudo python3 -m pip install git-review
```

CI emulator is **launched** by executing the `docker-compose up` command in the same directory as the `docker-compose.yaml` file. If you get some errors, try executing with `sudo`.

It might take a couple of minutes for everything to finish preparing but you can check the current status of your CI by visiting http://localhost:9000/t/core-tenant/status. Here you will be able to see all the jobs currently running in your pipelines. Terminal instance in which you launched the `docker-compose up` command will stay attached to the containers and display any output messages. You can shut down the CI emulator by pressing `Control+C` in the terminal. Don't worry all your work is safely stored in the containers and will reappear when you start the containers again with `docker-compose up`.

Once everything is up and running you should be able to access Gerrit here: http://localhost:8080/. If you go to BROWSE -> Repositories you will see a couple repositories, one of which is [proving-grounds](http://localhost:8080/admin/repos/proving-grounds).  This repository hold all the tasks you need to complete for the project.
## How to work with your local repository

 1. On the first run, you will have to create and set up a Gerrit user in order to clone the repository and commit code. This is covered in the **Add Your Gerrit Account** section of [Quick-Start Installation and Tutorial](https://zuul-ci.org/docs/zuul/tutorials/quick-start.html).
 2. You can clone your local repositories using http (ssh doesn't work in this case) by executing `git clone http://localhost:8080/<repository_name>` such as `git clone http://localhost:8080/proving-grounds`.
 3. You can't directly merge your code to the master when pushing to Gerrit. Instead of pushing your commit with `git push` you can  instead stage your changes up for a review with `git review` (you will have to confirm the connection and specify the user on your first run). After that your change should appear on Gerrit.
 4. You will find all open changes here http://localhost:8080/q/status:open. In order for your change to merge it needs to get approved in these three categories:
	 * **Code-Review** (approved by the reviewer, which means you)
	 * **Verified** (approved by Zuul)
	 * **Workflow** (approved by the reviewer, which means you)
 5. In order to approve a change you can click the **Reply** button in current change window and there you will see an option to grade Code-Review (+2 means approved) and Workflow (+1 means approved). Verify category is approved by Zuul if all you jobs in check pipeline succeed.
 6. You can manually trigger pipelines by writing keywords such as `recheck` or `regate` in the **Reply** window.
 7. After your change has been approved Zuul will automatically trigger the gate pipeline and merge your code if all the jobs in gate pipeline succeed.
 8. If your change has issues and you need to upload a patch you can do it ussing by ammending your commit. Checkout to the branch on which you pushed your change. Add all changes with `git add` and then execute `git commit --amend` which open your last commit message. If you want you can update the commit message but make sure you don't change the **Change-Id** number. After that you can push your patch using `git push origin HEAD:refs/for/<branch_name>`. At this point you will be asked to enter your gerrit username and http password. If you haven't already, you can get your http password in your **User settings** under **HTTP Credentials** section.


## Useful commands

* `docker container ls` - lists all running containers
* `docker container stop <name>` - stops a certain container
* `docker container prune` - deletes all stopped containers (does not delete data stored in the containers volume)
* `docker volume prune` - deletes all unused volumes (this will delete all data which was stored in the containers such as your repository)