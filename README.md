# Node.js - Grunt - Bower 

This is a simple container that makes it easy to run a Grunt-based workflow for applications, and includes Bower for
helping download JavaScript assets.

This container allows you to run most Grunt and Bower commands without having to install them on the host system, as 
well as provide a way to encapsulate the commands on the host if you are using a virtualization layer like Vagrant that 
can have very slow file notification times through NFS or VBoxFS.

This image can be found on the Docker Hub at [https://hub.docker.com/r/dragonmantank/nodejs-grunt-bower/](https://hub.docker.com/r/dragonmantank/nodejs-grunt-bower/)

## How to use this image

For basic usage you will want to run the container as yourself and mount your code inside the container. You will then
call one of the four installed programs (node, npm, grunt, bower) with whatever arguments are needed. For example, to 
run a `grunt watch` command, `cd` into the directory of your `Gruntfile.js` and run the following:

    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower grunt watch
    
`grunt watch` will then run as per your Gruntfile instructions. You should also be able to do other grunt targets like
`grunt build` and the like as well, as defined in your Gruntfile.

You can also manage your npm packages with commands like `npm install` or `npm update`. 

    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower npm install request
    
Since it supports npm and can write as your user back to the host file system, you can use it with your `package.json` files:

    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower npm install --save request
    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower npm install
    
You can update your npm package as well:
    
    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower npm update
    
Want to install Bower components? This works like you expect.

    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower bower install jquery
    
Since the container is based on node, it _should_ run your node scripts as well. We can invoke the following script:
 
```javascript
// script.js
var request = require('request');
request('http://ctankersley.com', function(error, response, body) {
    if (!error && response.statusCode == 200) {
        console.log(body);
    }
});
```

by calling node and passing it our script:

    docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower node script.js
    
I cannot make any guarantees about using the node binary for more complicated applications like full web applications,
since this container is meant to just help get around working with things like grunt and bower.
    
I would recommend setting up some aliases in your shell to make things easier. Some sample aliases might be:

    alias node="docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower node"
    alias grunt="docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower grunt"
    alias npm="docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower npm"
    alias bower="docker run -ti --rm -u $UID -v `pwd`:/data dragonmantank/nodejs-grunt-bower bower"
    
## Extending this Image

Simple create a new Dockerfile and use this image as your base image.

## Notes

I'm not a huge JavaScript programmer and generally use NodeJS when the need arises. If I've done anything massively wrong,
feel free to submit a pull request for changes.

I have moved the `$HOME` directory to `/data` inside the image to get around some permission issues, since this image
is meant to be run as your local user to help preserve file permissions. You may notice `.cache/`, `.config/`, `.local/`,
and `.npm/` folders appear in your directories because of this. Unfortunately I don't have a good way around this at
the moment, so make sure to add those folders to your `.gitignore` or appropriate version control ignore file.

## License

This Dockerfile is released under the MIT license, which should have accompanied the Dockerfile. Files and
binaries contained inside the image may contain other Licenses. 