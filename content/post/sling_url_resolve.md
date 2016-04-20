+++
bigimg = ""
date = "2016-04-19T22:10:39-03:00"
subtitle = ""
title = "Sling URL Resolve"

+++

Sling URL Resolve
===

When we are at the web, acessing pages and content, the URL is the definitive resource which inform what indeed
we're consuming.
In the programming world, usually when we're using languages and technologies such as PHP, Ruby, NodeJS, the URL
points out to a resource, but in the Sling world, this is different.

## Content as the protagonist

As commented previously, in the Sling world, differently from the main technologies that we see out there, the content
is the main entry point of the application.
When the framework is resolving the URL, it will try to match the content that is contained within the CRX Repository and,
just when the content is found, it will fetch it's properties in order to discover which script will be rendered.

The resolver will try to match the content in the URL by first trying to find in the content repository by the full
path of the URL, with the html extension. If the content isn't found, it will then try to find it without the extension.

So, for example, the url ```http://localhost:4502/content/testing.html```, it will first try to find in the repository for
```/content/testing.html``` and, as this node doesn't exist, it will fallback to ```/content/testing```.

*P.S.: This part of the URL resolving is described like this in the official documentation, but if you try to create
a page node with the name ```/content/testing.html``` directly, the resolve doesn't match it, I'm trying to discover if
there is any detail that perhaps I've missed, but I will update this post with this detail.*

### Finding the right script

When the content is finally found, the next step in the evaluation order of the resolver is to find the right script
to render the content of the resource in for the final user.
In order to do so, the resolver will fetch the node from the repository and inspect it's properties trying to find one
of those properties, in order:

- sling:resourceType
- sling:resourceSuperType
- jcr:primaryType

When the first is not found, it will fallback to the second and so on.

With the property found, it will parse it's value, for example ```blog/test``` and will try to find for this script in
two places, also in the same fallback order as the previous list:

- /apps
- /libs

## Nothing better than examples

**TODO**