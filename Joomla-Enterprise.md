The CMS wars seem to have had a unique side effect - There is more and more use of Joomla as a compelling alternative to build enterprise level software. At Techjoomla, we have seeing an increasing shift towards building large applications on top of Joomla, rather than websites. While the CMS features of Joomla are laudable, it brings along its own set of challenges, especially for scaling. This blog series chronicles the challenges posed and the ingenious solutions implemented to overcome these challenges, to build (nearly) 12 factor apps with Joomla.

We'll pick each of the 12 factors and a few more through the course of the series.

## CI / CD
Setting up a robust delivery pipeline has tremendous benefits. If you havent set up a automated build pipeline, I would urge you to do that today, and thank me later ! Implemented right, this promotes teams to merge and deploy code frequently, which means issues can be caught and fixed faster.

We've had a great experience with using Jenkins + Ansible to automate our delivery pipeline. 


## Session Management
With a scalable app, you want to ensure that sessions do not become a bottleneck. If your Joomla application is configured to scale out behind a load balancer it's important to note the choice, since it has a big impact on how your users log in. The most common one is to use files for session storage. In this case you would want to ensure that you enable sticky sessions. Most cloud providers, and even NGINX allows you to set up sticky sessions. 

However the most preferred way would be to use a memcache server for session storage. If that's not possible, store sessions in the database. You can change the database engine of the sessions table to Memory to improve performance if you see your sessions table frequently crashing.

## Scaling storage
With a single server scenario, you're usually not bothered about where extensions store uploaded files. You may have a social extension storing files in media and a ticketing extension uploading its files into its own component folder. However in case of a scaling out scenario this setup poses a big threat, since uploaded files end up sitting only on the server that a user gets connected to. The other servers do not have the files and start throwing errors. 

TO avoid this, it's important to set up all your extensions to store their files in a predictable location. Most extensions allow configuring the storage location and there's a good reason to that !

A typical scaling setup would consist of a NFS storage that is attached to all the nodes behing a load balancer. The NFS is mounted in a single folder, say `assets` in your Joomla root. Your should now configure all extensions to upload files somewhere inside of assets.

## Choice of extensions
This one is key since it directly affects the maintainability of your product. The most important piece to note here is to try and use extension that closest to the Joomla MVC pattern. Several extensions introduce their own way of implementing templates and funtionality that deviate from Joomla. In the long run these are hard to maintain, and you need to keep training your team to maintain these. Upgrades for these extensions is also a challege since it may break things that you dont easily notice.

Some more key things to look out for 
- Ensure extensions allow you to configure the path where user uploaded assets are stored. 
