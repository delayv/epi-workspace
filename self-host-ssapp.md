## BACKUP ALL YOUR WORKSPACE BEFORE REBUILDING ANYTHING

## Cheatsheet to build minimum workspace to host authentication feature SSAPP
```sh
git clone http://github.com/pharmaledger-imi/epi-workspace
cd epi-workspace
npm run dev-install
npm run server
```

leave the "server" running and open a new terminal

```
npm run install-mobile
npm run install-dfm
npm run install-acf-ssapps
```

Here everything is prepared for domain `demo.epi.alp` but you should adapt to suit your needs.   
1. Make necessary corrections to file [apihub-root/external-volume/config/bdns.hosts](apihub-root/external-volume/config/bdns.hosts) by adding an bdns entry with the correct domain name. 
2. Add a file in [apihub-root/external-volume/config/domains](apihub-root/external-volume/config/domains). Take file [demo.epi.alp.json](apihub-root/external-volume/config/domains/demo.epi.alp.json) as template.
3. change `"VAULT_DOMAIN":"vault"` to `"VAULT_DOMAIN":"{your_b_domain}"` in [env.json](env.json)

```sh
# npm run build-mobile # you don't need this except if you later want to build native app / use pwa 
npm run build-acf-ssapps # this command run also `npm run build-dfm`
# if the command above fails, try running `npm run build-all` before

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

## miscs
### building only the SSAPP
when rebuilding only the authentication feature SSAPP (ie: `cd {your_ssapp_dir} && npm run build`), *DO NOT FORGET TO PASS THE DOMAIN TO* `buildDossier` script, meaning you have to change the command to:  
`cd {your_ssapp_dir} && npm run build -- --domain={your_b_domain}`

### keeping the same keySSI
When restarting over from a fresh clone, copy the full content of `{PREVIOUS_WORKSPACE}/apihub-root/external-volume/` direct after clone command, before doing anything else. You may skip `apihub.json` file because the newer one may contain some new configs that you may not have.  
**Observation:** even with the above procedure the keySSI was not kept last time. It may be because `npm run build-acf-ssapps` erases the data at some point, or it may be because the old workspace had the keySSI "harcoded" in its `seed` file. With the latest build made, the `seed` file contains `ssi:alias:$VAULT_DOMAIN:delayv-auth-feat-ssapp::v0` .