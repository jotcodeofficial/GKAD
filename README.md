# Introduction
Welcome to the Gitlab Kubernetes Application Deployer (GKAD).


# Quick reference
* Where to file issues: [https://github.com/jotcodeofficial/GKAD/issues](https://github.com/jotcodeofficial/GKAD/issues)


# What is GKAD?
GKAD is an opinionated application framework that lays the groundwork so that you can deploy your applications to your kubernetes cluster using a gitlab-ci.yml file. This gets you off the ground fast and lets you have reliable builds with continous deployment.

For more information and related blog posts, please visit [Jotcode.com](https://jotcode.com).

# How to get started
Hey there, Lets begin!

First you need to get two clusters. One for staging and one for production.
Generally you would want your production cluster to be a bit beefier since it will be used by your target market. You should do some research on what kind of applications you will be deploying to ensure your cluster will meet your needs.

Once you have your clusters you can log into your Gitlab account and add these clusters. Ensure that you select that these will be unmanaged clusters, this is because we will deploy all applications ourselves. You can do managed if you wish, but I prefer unmanaged. Call the clusters: `staging` and `production`. Now it's time to install the Gitlab runner on each of your clusters.
Go here and follow the instructions to install the helm chart on each cluster:

[Full tutorial coming soon]
[https://artifacthub.io/packages/helm/gitlab/gitlab-runner](https://artifacthub.io/packages/helm/gitlab/gitlab-runner)

Once your runners are operational you can install Traefik, ensure the service type is LoadBalancer.

[Full tutorial coming soon]
[https://artifacthub.io/packages/helm/traefik/traefik](https://artifacthub.io/packages/helm/traefik/traefik)

Then within your Gitlab account you should see your cluster load balancer generated address for both staging and production. You can go to your domain provider and change your records to these for both `staging.domain.com` and `domain.com`

Now you can also install Cert Manager:

[Full tutorial coming soon]
[https://artifacthub.io/packages/helm/jetstack/cert-manager](https://artifacthub.io/packages/helm/jetstack/cert-manager)

So at this stage you should have two clusters ready to go that are connected to your Gitlab account.

Now it is time to fork this repo:
[https://gitlab.com/Jotcode/GKAD](https://gitlab.com/Jotcode/GKAD) into your Gitlab account. Then whenever you commit a change to the gitlab-ci.yml file it will start the build process. If you want to keep your source on Github you can fork the Github version:
[https://github.com/jotcodeofficial/GKAD](https://github.com/jotcodeofficial/GKAD)


## Varaibles to Change

### `gitlab-ci.yml > HELM_KUBE_IMAGE_VERSION`
This is the version of a docker image that we use which lets you run helm and kubectl commands.
### `gitlab-ci.yml > STAGING_DEPLOY_TYPE`
This should be set to `install` if you want to deploy to your staging cluster, otherwise set to `nothing`.  

### `gitlab-ci.yml > PRODUCTION_DEPLOY_TYPE`
This should be set to `install` if you want to deploy to your staging cluster, otherwise set to `nothing`, the default should be `nothing` until you can confirm your application works on your staging cluster.    

### `gitlab-ci.yml > STAGING_INSTALL_STORGAGE` 
This should be set to `install` if you want to deploy a storage volume to your hosting provider for your staging cluster, otherwise set to `nothing`. The storage volumes are used to contain your application files.   

### `gitlab-ci.yml > PRODUCTION_INSTALL_STORGAGE` 
This should be set to `install` if you want to deploy a storage volume to your hosting provider for your production cluster, otherwise set to `nothing`. The storage volumes are used to contain your application files. By default this is set to `nothing` until you can confirm your staging application works first.    

### `staging-storage > storageClassName`
This is the hosting provider we are using for our staging cluster. In this example we are using digital ocean which uses the `do-block-storage` keyword.

### `storage > storageClassName`
This is the hosting provider we are using for our production cluster. In this example we are using digital ocean which uses the `do-block-storage` keyword.

### `staging-cronjob > command`
### `cronjob > command`
You can add commands here to the cron job.

### `staging-cronjob > schedule`
### `cronjob > schedule`
You can update the schedule for the cronjob to be ran here.

### `staging-solver.yml`
### `solver.yml `
### `staging-secure-ingress-route.yml `
### `secure-ingress-route.yml`
### `staging-ingress-route.yml`
### `ingress-route.yml`
In these files you need to update the domains to your actual domains.

### `staging-issuer > email`
### `issuer > email`
Update the email field to your own email in these files.

### `staging-application > SSH_KEY`
### `application > SSH_KEY`
Update the ssh field to your own repos ssh key in these files.

### `staging-application > GIT_REPO`
### `application > GIT_REPO`
Update the repo field to your own github repo in these files. The application code in this repo will be pulled and put into this container when deployed.

### `staging-application > GIT_EMAIL`
### `application > GIT_EMAIL`
Update the email field to your own git email in these files.

### `staging-application > GIT_NAME`
### `application > GIT_NAME`
Update the name field to your own git name in these files.

### `staging-auth-secret > users`
### `auth-secret > users`
The users option is an array of authorized users. Each user must be declared using the name:hashed-password format. Follow the guide here:
[https://doc.traefik.io/traefik/middlewares/basicauth/](https://doc.traefik.io/traefik/middlewares/basicauth/)

Note: in a kubernetes secret the string (e.g. generated by htpasswd) must be base64-encoded first.
To create an encoded user:password pair, the following command can be used:
``` htpasswd -nb user password | openssl base64 ```





When your application has deployed after forking the project, making changes and updating the gitlab-ci.yml file to your project, the build process should start. You should be able to see the read out to follow the process. When it completes you can check to see if all the services are running by:

```console
$ kubectl get all -n applications
```

Ensure to check the storage volume was created in your provider.

Your app should now be visible by visiting your staging.domain.com

Once you can confirm everything is working as expected, you can edit the gitlab-ci.yml file and enable the production builds.

For more information on configuring the phpfpm application, check out:
[https://gitlab.com/ric_harvey/nginx-php-fpm](https://gitlab.com/ric_harvey/nginx-php-fpm)

If you want to use your own docker container then just change the relevant code in the staging-application.yml and application.yml files