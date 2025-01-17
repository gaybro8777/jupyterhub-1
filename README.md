# Running the NASA Cloud Workshop notebooks with mybinder.org

Anyone can click this badge to open the repo in MyBinder.org. You may have to click it twice because the installation takes awhile and might hang. Cost free.

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/eeholmes/jupyterhub/main)

This will open the repo in JupyterLab and you can run the NASA Cloud workshop Jupyter Notebooks that are in the tutorials folder.
Everything should work except direct S3 access of data. That requires you to be on a hub in AWS region us-west-2 and mybinder.org is not on that.

<img src="images/mybinder.png" width="500"/>

**How do you set up your own Binder JupyterHub?** Fork or clone this repo, copy the url of your repo, paste your URL into the box in [MyBinder](https://mybinder.org/), and then copy the link it gives to your Binder JupyterHub. That's it. Literally. The key bit is the `environment.yml` files which says what Python version (if needed) and packages to put in your Python environment.

# Running with Pangeo Binder

As noted above, MyBinder.org is not on AWS us-west-2 so you are limited in some of your cloud computing options with NASA EarthData. For example S3 access requires that you are on AWS us-west-2. You can use [Pangeo Binder](https://aws-uswest2-binder.pangeo.io/) instead. It operates the same as MyBinder. Fork/clone this repo, copy the URL of you new (cloned) repo, paste that URL into the box on Pangeo Binder. That's it and its free. Note, your file changes won't be persistent (= you will lose your changes when you finish) so make sure to download the results of whatever you are doing.

# Setting up a JupyterHub on AWS

Another option is to set up your own "little" JupyterHub on AWS to provide a virtual coding platform. Users will have their own environment where they can save their notebooks and do a project. This is a good option for small workshops (10-20) where users will not need too much computing power or storage space. You'll upgrade your AWS instance to something large for the workshop and then (delete user files) and downsize after the workshop is done. The instructions here were inspired by this post by someone who uses a JupyterHub for similar kinds of workshops. https://outpw.github.io/tutorials/JupyterHubSetup.html

Cost: The free tier, t2.micro, should be fine for set-up. I think you can leave it on without cost since you get 750 hours per month free. For a recent workshop, where we used t2.small and t2.medium, the cost came out to $100 per day for 65 users, so ca $1.55 per user per day. But that was on 2i2c.org. I have a t2.small instance (2 Gig RAM, 2 CPU, 30 gig storage) running continuously and it is costing 50 cents per day (so $15 per month). I'll stop it once I am done testing.

**Overall** setting up a virtual machine and JupyterHub on AWS was easy and I had never done it before. Installing TLJH ("the littlest jupyter hub") was easy on a t2.micro instance (but didn't work on non-free instances) and worked seamlessly. Customizing my hub to match the NASA hackathon environment was unsuccessful until I learned that a) 8 Gig storage was too small, b) 1 Gig RAM (t2.micro) was too small and c) I had to install TLJH on t2.micro and then upgrade my instance to t2.small before doing anything else. But once I upgraded to a t2.small and more storage, installation worked easily.  [Read my copious notes on how I successfully set up the hub on the wiki](https://github.com/eeholmes/jupyterhub/wiki)

## Basic steps

1. Follow the [TLJH instructions](https://tljh.jupyter.org/en/latest/install/amazon.html) to install TLJH on a t2.micro instance (I have not had success in installing on anything other than t2.micro). Set the storage to the max for the free tier (30 Gig).
2. Wait for 10-15 min, and then try to log into your JH. Use the public IP (you'll find that listed on the AWS dashboard under EC2 instance). `<public.ip>/hub/login` Use your AWS username and your root password. Log out.
3. Go back to the AWS dashboard. Stop your instance, and go to instance setting and upgrade to t2.small. Restart your instance. You'll have a new public IP. See note below for setting a fixed IP address.
4. Log into your JH with the new public IP. Copy [environment.yml](https://github.com/eeholmes/jupyterhub/blob/main/environment.yml) into a file at the base level (not in a folder). Note this is the environment used for the [NASA Cloud hackathon](https://nasa-openscapes.github.io/2021-Cloud-Hackathon/) with a few small changes needed for a TLJH JupyterHub set-up.
5. Open a terminal in your JupyterHub--if you are in notebook format, look for "New" on the right. If you are in lab format, look in the File > New dropdown menu. In the terminal, run this code `sudo -E mamba env update -f environment.yml`. Might take awhile to install the environment. **Make sure you have at least 16 Gig of storage and have 2 Gig RAM**, i.e. don't try this on the default free tier of t2.micro with 8 Gig of storage.
6. Once the new environment is installed (into base), go to the hub main page, click the Admin tab and add some users. They will choose a password when they log in.
7. Go to the [wiki](https://github.com/eeholmes/jupyterhub/wiki) to see how to set up your connection to GitHub.

Optional

*  If you want the default UI to be JupyterLab instead of notebook, run `sudo tljh-config set user_environment.default_app jupyterlab` from your JupyterHub admin account (meaning log into your hub with your admin username).
*  Every time you stop your instance, the IP address will change. You can assign a fixed address easily. See the [wiki page re managing your instance](https://github.com/eeholmes/jupyterhub/wiki/5.-Managing-the-instance)
* Getting content (notebooks) into the hub for your students. One option is to have your students clone a GitHub repo with your content (notebooks), then have them pull into content from upstream (sync with upstream repo), and then pull the the updated GitHub repo content in the hub. That's a few steps. Another common option is to use  nbgitpuller to automatically pull in notebooks into a users JH account
https://tljh.jupyter.org/en/latest/howto/content/nbgitpuller.html#howto-content-nbgitpuller The downside of this approach is that the students will need to make copies of the content if they want to save it.


