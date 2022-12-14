# microfrontend
A basic micro front end app using Module Federation of Webpack
Git Monorepo contains all related projects in one Repo 
Amazon S3 and cloudFront (used to pick files from s3)
Invalidations: CDN can identify the changes in filenames, but not within the file Ex: index.html, remoteEntry.js. Therefore, invalidations are used

## Features

1) Zero coupling between child projects which means
No imports of functions/classes/objects/etc between child projects
No redux store, reducers, context is shared. The only exception is the shared library through module federation system

2) Near zero coupling between container (parent) and child apps
	Container shouldnt assume child is using any particular framework
	Important - Any necessary communication is done with callbacks or simple events

3) CSS from one project shouldn't affect another. It is 100% scoped


## AWS Cheatsheet

### S3 Bucket Creation and Configuration
Go to AWS Management Console and use the search bar to find S3

Click Create Bucket

Specify an AWS Region

Provide unique Bucket Name and click Create Bucket

Click the new Bucket you have created from the Bucket list.

Select Properties

Scroll down to Static website hosting and click Edit

Change to Enable

Enter index.html in the Index document field

Click Save changes

Select Permissions

Click Edit in Block all public access

Untick the Block all public access box.

Click Save changes

Type confirm in the field and click Confirm

Find the Bucket Policy and click Edit

Click Policy generator

Change Policy type to S3 Bucket Policy

Set Principle to *

Set Action to Get Object

Copy the S3 bucket ARN to add to the ARN field and add /* to the end.
eg: arn:aws:s3:::mfe-dashboard/*

Click Add Statement

Click Generate Policy

Copy paste the generated policy text to the Policy editor

Click Save changes


### CloudFront setup
Go to AWS Management Console and use the search bar to find CloudFront

Click Create distribution

Set Origin domain to your S3 bucket

Find the Default cache behavior section and change Viewer protocol policy to Redirect HTTP to HTTPS

Scroll down and click Create Distribution

After Distribution creation has finalized click the Distribution from the list, find its Settings and click Edit

Scroll down to find the Default root object field and enter /container/latest/index.html

Click Save changes

Click Error pages

Click Create custom error response

Change HTTP error code to 403: Forbidden

Change Customize error response to Yes

Set Response page path to /container/latest/index.html

Set HTTP Response Code to 200: OK


### Create IAM user
Go to AWS Management Console and use the search bar to find IAM

In IAM dashboard, click Users in the left sidebar

Click Add Users

Enter a unique name in the User name field

In Select AWS credential type tick Access Key - Programmatic access

Click Next: Permissions

Select Attach existing policies directly

Use search bar to find and tick AmazonS3FullAccess and CloudFrontFullAccess

Click Next: Tags

Click Next: Review

Click Create user

# Points to remember

* Github actions use yml files for CICD

* class-name collision
Prob: Two different projects using the same css-in-jss such as Material-UI, might get class-name collision
Why ? In production, classnames are shortened and can cause overlap of same classname which was not a problem in development mode
Soln ? StylesProvider, generateClassname - a (different) classname prefix for all projects is the soln

* History object
3 types: Browser history, hash history, memory history

* Host project uses Browser history
Better, sub projects use memory history otherwise race condition between browser history may occur

<BrowserRouter/> is a ReactComponent of React-router-dom, uses browser history and memory history
<Router/> is a ReactComponent of React-router-dom, uses memory history

* Have you ever used history.push('/d****d') ?
In the above scenario history objects of host and sub projects needs to be in sync, which can be achieved by callbacks and history.push
history.push is used to update the url with the pathname
