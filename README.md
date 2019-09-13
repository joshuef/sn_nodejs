# safe-nodejs

## Overview


| [MaidSafe website](https://maidsafe.net) | [SAFE Dev Forum](https://forum.safedev.org) | [SAFE Network Forum](https://safenetforum.org) |
|:-------:|:-------:|:-------:|

## Deploy

For electron run, `yarn neon-build-electron`, which will build for electron 6 as it stands.

Before uploading binaries you should ensure you remove `native/target` folder or that'll be collected and packaged up too. VASTLY increasing file size, needlessly.

Then `yarn upload binaries` will upload the current node version and an electron one too.

Releases are triggered on github via the presence of TRAVIS_TAG.


## Examples

```js
// This file is required by the index.html file and will
// be executed in the renderer process for that window.
// All of the Node.js APIs are available in this process.

const APP_ID = "net.maidsafe.safe_browser";
const APP_NAME = "SAFE Browser";
const APP_VENDOR = "MaidSafe.net Ltd";

const { Safe } = require('safe-nodejs');

let safe = new Safe();

// "Authorising application..."
let auth_credentials = safe.auth_app(APP_ID, APP_NAME, APP_VENDOR);

// "Connecting to the Network..."
safe.connect("net.maidsafe.safe_nodejs", auth_credentials);

let files_container = safe.files_container_create("testfolder/", "", false, false);
// FilesContainer created

let safe_data = safe.files_container_get(files_container[0]);
// "Fetched FilesContainer content: ", safe_data

// FilesContainer sync
safe.files_container_sync("testfolder/subfolder", files_container[0], false, false, false, false);
safe_data = safe.files_container_get(files_container[0]);
// "FilesContainer synced: ", safe_data

// PublishedImmutableData put
let buffer = Uint8Array.from([72, 101, 108, 108, 111, 32, 69, 108, 101, 99, 116, 114, 111, 110, 33, 33, 33, 10]);
let immd_url = safe.files_put_published_immutable(buffer.buffer);
// "PublishedImmutableData put: ", immd_url

// PublishedImmutableData get
safe_data = safe.files_get_published_immutable(immd_url);
// "Fetched PublishedImmutableData content: ", safe_data

// Create an NRS public name
let random = Math.floor(Math.random() * Math.floor(1000));
let nrs_name = `mypubname-${random}`;

// "Let's link NRS name to ", files_container[0]
let nrs_map_data = safe.nrs_map_container_create(nrs_name, `${files_container[0]}?v=0`, true, true, false);
// "NRS Map Container created: ", nrs_map_data

// Add a subname to the NRS public name
nrs_map_data = safe.nrs_map_container_add(`subname.${nrs_name}`, immd_url, false, true, false);
// "NRS Map Container updated: ", nrs_map_data

// Fetch NRS Map Container
nrs_map_data = safe.nrs_map_container_get(nrs_name);
// "NRS Map Container fetched: ", nrs_map_data

// Remove a subname from the NRS public name
nrs_map_data = safe.nrs_map_container_remove(`subname.${nrs_name}`, false);
// "NRS Map Container updated: ", nrs_map_data

// Let's parse a URL
let parsed_url = safe.parse_url(`safe://${nrs_name}`);
// "Parsed URL: ", parsed_url

// Let's parse and resolve a URL
parsed_url = safe.parse_and_resolve_url(`safe://${nrs_name}`);
// "Parsed and resolved URL: ", parsed_url

let url = `${files_container[0]}/test.md`;
// "Let's fetch content from ", url

safe_data = safe.fetch(url);
if (safe_data.PublishedImmutableData) {
  let str = String.fromCharCode.apply(null, safe_data.PublishedImmutableData.data);
  // "Fetched content: ", str
}

let url_v0 = `${url}?v=0`;
// "Let's fetch v0 of the content from ", url_v0

let safe_data_v0 = safe.fetch(url_v0);
if (safe_data_v0.PublishedImmutableData) {
  let str_v0 = String.fromCharCode.apply(null, safe_data_v0.PublishedImmutableData.data);
  // "Fetched content: ", str_v0
}

let url_root = files_container[0];
// "Let's fetch v0 of the content from ", url_root

let safe_data_root = safe.fetch(url_root);
if (safe_data_root.PublishedImmutableData) {
  // "I wasn't expecting that type of SafeData: ", safe_data_root
} else if (safe_data_root.FilesContainer){
  // "Fetched root raw content: ", safe_data_root
}
```


## License

This is dual-licensed under the Modified BSD ([LICENSE-BSD](LICENSE-BSD) https://opensource.org/licenses/BSD-3-Clause) or the MIT license ([LICENSE-MIT](LICENSE-MIT) https://opensource.org/licenses/MIT) at your option.

## Contribution

Copyrights in the SAFE Network are retained by their contributors. No copyright assignment is required to contribute to this project.
