# Rosemary: OpenStreetMap for Ruby

[![Gem Version](https://badge.fury.io/rb/rosemary.svg)](http://badge.fury.io/rb/rosemary)
[![Build Status](https://travis-ci.org/sozialhelden/rosemary.svg?branch=master)](https://travis-ci.org/sozialhelden/rosemary)
[![Dependency Status](https://gemnasium.com/sozialhelden/rosemary.png)](https://gemnasium.com/sozialhelden/rosemary)
[![Coverage Status](https://codeclimate.com/github/sozialhelden/rosemary/badges/coverage.svg)](https://codeclimate.com/github/sozialhelden/rosemary/coverage)
[![Code Climate](https://codeclimate.com/github/sozialhelden/rosemary.svg)](https://codeclimate.com/github/sozialhelden/rosemary)
[![License](https://img.shields.io/badge/license-MIT-green.svg) ](https://github.com/sozialhelden/rosemary/blob/master/LICENSE)
[![Gittip ](https://img.shields.io/gittip/sozialhelden.svg)](https://gittip.com/sozialhelden)

This ruby gem is an API client for the current OpenStreetMap [API v0.6](http://wiki.openstreetmap.org/wiki/API_v0.6). It provides easy access to OpenStreetMap (OSM) data.

## What is OpenStreetMap?

OpenStreetMap (OSM) is a collaborative project to create a free editable map of the world. Two major driving forces behind the establishment and growth of OSM have been restrictions on use or availability of map information across much of the world and the advent of inexpensive portable GPS devices.


## The OpenStreetMap Database

OpenStreetMap data is published under an open content license, with the intention of promoting free use and re-distribution of the data (both commercial and non-commercial). The license is [Open Database License (ODbL)](http://opendatacommons.org/licenses/odbl/) from [Open Data Commons (ODC)](http://opendatacommons.org/). Data users are required to give credit to OpenStreetMap. If you create a "derived database" from OpenStreetMap you are required to share this under the same license. [Read more about the OpenStreetMap License here](https://wiki.osmfoundation.org/wiki/License). 

If you are building a system which allows contributors to add/modify the OpenStreetMap database, then all contributors must register with the project and agree to the "[Contributor Terms](https://wiki.osmfoundation.org/wiki/License/Contributor_Terms)".

## Installation

Put this in your Gemfile

``` ruby
# Gemfile
gem 'rosemary'
```

Then run

``` bash
bundle install
```

## Getting started

OK, gimme some code:

``` ruby
require 'rosemary'
api = Rosemary::Api.new
node = api.find_node(123)
 => #<Rosemary::Node:0x1019268d0 @changeset=7836598, @timestamp=Mon Apr 11 19:40:43 UTC 2011, @user="Turleder'n", @tags={}, @uid=289426, @version=4, @lat=59.9502252, @id=123, @lon=10.7899133>
```

## Testing your code

You should try your code on the OSM testing server first! You can change the url like this:

``` ruby
require 'rosemary'
Rosemary::Api.base_uri 'http://api06.dev.openstreetmap.org/'
api = Rosemary::Api.new
api.find_node(123)
```

## Making edits to OpenStreetMap
Modification of data is supported too (a read/write API), but this requires authentication. According to the OSM license every modification to the data has to be done by a registered OSM user account (so that all contributors have agreed to the [Contributor Terms](https://wiki.osmfoundation.org/wiki/License/Contributor_Terms)).

If you are writing a script to perform imports or other kinds of automated editing, you should also be aware of [Automated Edits code of conduct](http://wiki.openstreetmap.org/wiki/Automated_Edits_code_of_conduct). Please read this before starting work on a such a script. It includes many important things to be aware of. It also includes the requirement to register a separate OpenStreetMap user account for such activity.

The OpenStreetMap user can be authenticated with username and password. But see yourself:

``` ruby
client = Rosemary::BasicAuthClient.new('osm_user_name', 'password')

api = Rosemary::Api.new(client)
changeset = api.create_changeset("Some meaningful comment")
node = Rosemary::Node.new(:lat => 52.0, :lon => 13.4)
api.save(node, changeset)
api.close_changeset(changeset)
```

Yeah, I can hear you sayin: 'Seriously, do I have to provide username and password? Is that secure?' Providing username and password is prone to some security issues. But OpenStreetMap supports secure HTTPS connections to hide basic auth headers. But wait, there is some more in store for you: [OAuth](http://oauth.net/) It's much more secure for the user and your OSM app. But it comes with a price: You have to register an application on http://www.openstreetmap.org. After you have your app registered you get an app key and secret. Keep it in a safe place.

``` ruby
consumer = OAuth::Consumer.new( 'osm_app_key', 'osm_app_secret',
                                :site => 'http://www.openstreetmap.org')
access_token = OAuth::AccessToken.new(consumer, 'osm_user_token', 'osm_user_key')
client = Rosemary::OauthClient.new(access_token)

api = Rosemary::Api.new(client)
changeset = api.create_changeset("Some meaningful comment")
node = Rosemary::Node.new(:lat => 52.0, :lon => 13.4)
api.save(node, changeset)
api.close_changeset(changeset)
```

Every request to the API is now handled by the OauthClient.


## Feedback and Contributions

We appreciate your feedback and contributions. If you find a bug, feel free to to open a GitHub issue. Better yet, add a test that exposes the bug, fix it and send us a pull request.
