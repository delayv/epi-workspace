#### Cheatsheet to build minimum workspace to host authnetication feature SSAPP
```sh
git clone http://github.com/pharmaledger-imi/epi-workspace
cd epi-workspace
npm run dev-install
npm run install-mobile
npm run install-dfm
npm run install-acf-ssapps
```

Here everything is prepared for domain `demo.epi.alp` but you should adapt to suit your needs.   
1. Make necessary corrections to file [apihub-root/external-volume/config/bdns.hosts](apihub-root/external-volume/config/bdns.hosts) by adding an bdns entry with the correct domain name. 
2. Add a file in [apihub-root/external-volume/config/domains](apihub-root/external-volume/config/domains). Take file [demo.epi.alp.json](apihub-root/external-volume/config/domains/demo.epi.alp.json) as template.
3. You need to add `-- --domain={your_bdomain_name}` in the following tasks in [octopus.json](octopus.json) and [octopus-freeze.json](octopus-freeze.json) :  
    3.1 Task `prepare-build-mobile` for webcardinal  
    3.2 Task `build-acf-ssapps` for all the authentication feature SSAPPs you want to host

```sh
npm run build-mobile
npm run build-acf-ssapps
#at this point you normally we switch to Xcode and build your native app, but here we don't need that
#we could return to workspace and prepare the dsu-fabric wallet and create some products to test after
#npm run build-all
#but actually we don't want that, since the enterprise wallet will be provided by bdomain 'epi'
```

At this point everything is ready. You need to make this server reachable by the enterprise wallet and the ios app you want to use. Do not forget that once built, you can change the port used in file [apihub-root/external-volume/config/apihub.json](apihub-root/external-volume/config/apihub.json). It seems safer to change it only after having issued all `npm run build-*` commands and so keep port 8080 when building.  
In the epi-workspace used to provide enterprise-wallet and os app, you will need to add:
```json
"demo.epi.alp": {
    "replicas": [],
    "brickStorages": [
      "{the_accessible_url_without_trailing_slash}"
    ],
    "anchoringServices": [
      "{the_accessible_url_without_trailing_slash}"
    ],
    "notifications": [
      "{the_accessible_url_without_trailing_slash}"
    ]
  },
```

In [apihub-root/external-volume/config/bdns.hosts](apihub-root/external-volume/config/bdns.hosts) and/or [mobile/config/scan-app/ios/PSKNodeServer/PSKNodeServer/nodejsProject/apihub-root/external-volume/config/bdns.hosts](mobile/config/scan-app/ios/PSKNodeServer/PSKNodeServer/nodejsProject/apihub-root/external-volume/config/bdns.hosts) depending on your use-case.