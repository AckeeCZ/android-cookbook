# Release process
In Ackee we have a well defined process of releasing the new version of the app. It was not always a case in the past and we've suffered from couple of problems like
- Developer built the app locally on his workstation and did not know the project really well and built a wrong combination of flavors/build types. 
- He had some files locally that were not in VCS and it lead to not reproducible builds.
- He forgot to upload `mapping.txt` to our crash reporting tool and the crashes were obfuscated. Also this `mapping.txt` was probably lost forever.
- He did not marked this point in VCS as a release and it was really hard to guess which one of the commits was the release one to reproduce a bug that occurred.
- Version of the app was not increased and it was ambiguous in which version to look up the bug/crash. 
- We have found that the url for our apps contains package name with pattern like `cz.ackee.nameoftheapp`. This is a little embarassing, because at first it may look like a good advertising for our company but in reality its a sign of amaterism. Its not that rare that the client will take the app from one agency to another or he will create an internal team but now he is stucked with our name there because Play store does not allow change of this package name.

Several of them are not that problematic, couple of them are but we eliminated most of that with automated/well described process of the release. 

### CI
The crucial part is that we have for every app so called "Release job" in our Jenkins CI. Build configuration in this job is the source of truth and all the changes should be made there. 
Resposibilities of the job are:
- it knows combination of flavors/build types, build properties to always produce the right Apk.
- it always fetches the actual code from Git from `master` branch
- it uploads produced apk to HockeyApp with the `mapping.txt`
- it archives the apk and `mapping.txt` to the current build 

This is our current automated list and we would like to improve it with stuff like automated tagging or Play store upload so it lies ahead of us in the near feature.

### Manual process
There is still some manual work that we have to do and is not (yet) automated.

- the app version has to be increased eg. 2.0.0 -> 2.0.1. We increase even versions that are not yet fully rolled-out so its easily distinguishable which version is which. 
- the code has to be merged into master branch 
- the tag has to be created with the version name in its name, like `v2.0.1`
- upload of apk to play store

as mentioned in previous sections, couple of this task will be automated 

### Play store upload
In last couple of years the Play store massively improved its release management with stuff like Alfa/Beta channels, staged rollouts, internal test tracks and pre launch reports. We try to leverage as much as possible from that and thats why our typical upload process looks like
- grab the Apk from the Jenkins release job and upload it to **Internal test track**. Ideally retrieve release notes from the manager and fills them. but they can be changed later.
- let the play store perform [Pre-launch testing](https://support.google.com/googleplay/android-developer/answer/7002270?hl=en) and provides you reports from that. That takes couple of hours to do completely
- check for possible issues - crashes, ANRs, memory management, performance. ..
- if all is good, proceed, if not, investigate problems, evaluate that and resolve
- unfortunately its not possible to move from internal track directly to Production, so its needed to move this version to Alfa channel and then to production
- perform staged rollout of the app, typically Play store ofers percentages itself so try to keep it
- in the next days, check for problems, if any, fix them, if not, increase percentages until 100% 

### Checklist
If the manager wants to release new version of the app this is the checklist that should be step-by-step performed
- [ ] If first release, double check if the package name of the app does not contain *ackee*. 
- [ ] Check if release job exists in Jenkins 
- [ ] If first release, try to build release build type locally to check if minification is turned on and works correctly
- [ ] Increase version name of the app, eg. 2.0.0 -> 2.0.1
- [ ] Merge code from `development` branch to `master`
- [ ] Create tag with the version name in it, `v2.0.1` and make sure that you pushed that code
- [ ] Go to Jenkins and let him build your app
- [ ] Take the apk and try to install it if everything works. Ideally perform app update test - install previous version, turn it on, use it a little and then install new version and try it.
- [ ] Grab the apk and upload it to Internal track
- [ ] Wait for pre-launch tests to finish and evaluate results. If anything serious, fix it and create a new version.
- [ ] Based on requirements, move version to alfa/beta channel and leave it there or if it should be production release, move it next to production.
- [ ] Perform staged rollout and in the next days periodically check if everything is ok until 100% rollout is reached.

