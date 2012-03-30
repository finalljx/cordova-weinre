<!--
 * Licensed to the Apache Software Foundation (ASF) under one
 * or more contributor license agreements.  See the NOTICE file
 * distributed with this work for additional information
 * regarding copyright ownership.  The ASF licenses this file
 * to you under the Apache License, Version 2.0 (the
 * "License"); you may not use this file except in compliance
 * with the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing,
 * software distributed under the License is distributed on an
 * "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
 * KIND, either express or implied.  See the License for the
 * specific language governing permissions and limitations
 * under the License.
-->

weinre is WEb INspector REmote.

Pronounced like the word "winery". Or maybe like the word "weiner". 
Who knows, really.

weinre is a debugger for web pages, 
like FireBug (for FireFox) and Web Inspector (for WebKit-based browsers), 
except it's designed to work remotely, and in particular, 
to allow you debug web pages on a mobile device such as a phone.

For more information on weinre:
[http://http://incubator.apache.org/cordova/](http://incubator.apache.org/cordova/)

weinre source
-------------

The weinre source is contained in 4 subdirectories:

* `weinre.build` - contains the tools to build weinre, the 3rd party libraries
that weinre uses, and holds the output of the build

* `weinre.doc` - source for the HTML manual for weinre

* `weinre.server` - code for the node.js-based weinre server

* `weinre.web` - code for the client and target pieces of weinre


building weinre
---------------

The weinre build is currently run on a Mac OS X 10.7 laptop.  It also runs on 
Apache continuous integration servers running Linux.  The build is not 
typically run on Windows, so if you have problems with that, please log an 
issue.  

The weinre build pre-req's the following tools:

* node.js - [http://nodejs.org/](http://nodejs.org/)
* ant - [http://ant.apache.org/](http://ant.apache.org/)

To update the npm-based pre-reqs, you will also need:

* npm - should be shipped with node.js, on Linux may need to be installed as a 
separate package 

Before doing a weinre build, you will need to create the file
`weinre.build/personal.properties`.  Use the `sample.personal.properties` as a 
template. The build should fail if this file is not available.

To update the version label of weinre, edit the file 
`weinre.build/build.properties`.  If the version has a `-pre` suffix, this 
triggers the build to artifacts with timestamped names.  For an 'official' 
build, do not use the `-pre` suffix.

There are two ways to build weinre:

* full build
* development build

The full build creates all the artifacts needed for an 'official' build.

The development build just creates enough artifacts to test the code.

### the first time you run any build: ###

Some semi-transient artifacts are created the first time you run a build.
These will be stored in the `weinre.build/cached` directory.

### to perform the full build: ###

* run: `cd weinre.build`
* run: `ant build-archives`

This will run the development build (see below), and then create zip archives 
of the build in the `weinre.build/out/archives` directory.

### to perform the development build: ###

* run: `cd weinre.build`
* run: `ant`

This will populate a number of resources in the `weinre.server` directory, so 
that you can run weinre directly from that directory for testing.  It does not 
build the archives.

### performing a clean build: ###

* run: `cd weinre.build`
* run: `ant clean`
* perform the build as usual

### to run the output of the development build: ###

* run: `cd weinre.server`
* run: `./weinre [your flavorite options]`

### other fun development-time hacks ###

If you have the [wr tool](https://github.com/pmuellr/wr) installed, there are
two `.wr` files available to run the two builds.  If you run `wr` in the 
top-level directory, a full build is performed when the source changes.  If you 
run `wr` in the `weinre.build` directory, a development-time build is 
performed.

The build is growl-enabled, so you can see a quick message when the build 
completes.

The command `weinre.server/weinre-hot` makes use of
[node-supervisor](https://github.com/isaacs/node-supervisor) to re-launch the
weinre server generated by the development build, whenever a weinre build
completes.

Putting this altogether, you can open two terminal windows, run `wr` in the 
`weinre.build` directory to have a development build run whenever you change 
the source, and then run `weinre-hot` in the `weinre.server` directory to have 
the weinre server restart whenever a build completes, getting a growl 
notification at that time.

updating 3rd party libraries
-----------------------------

> **IMPORTANT** - All 3rd party libraries are stored in the SCM, so that the 
build does not require 3rd party packages to be downloaded.  As such, these 
files need to be ok to use and store in the SCM, given their licenses.  If 
you're adding or updating a 3rd party library, make sure the license is 
acceptable, and add/update the license in the top-level `LICENSE` file.

All of the 3rd party dependencies used with weinre are stored in one of two 
directories:

* `weinre.build/vendor` - contains libraries used in the client and/or target,
as well as libraries used by the build itself

* `weinre.server/node_modules` - contains npm packages used by the weinre.server

To update the files in `weinre.build/vendor`:

* edit the file `weinre.build/vendor.properties` as appropriate
* run: `cd weinre.build`
* run: `ant -f update.vendor.xml`

To update the files in `weinre.server/node_modules`:

* edit the file `weinre.build/package.json.template` as appropriate
* run a build (see above), so that the file `weinre.server/package.json` file is created
from the template you edited above
* run: `cd weinre.server`
* run: `npm install`