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
### Play store upload

### Checklist
