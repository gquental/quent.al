+++
bigimg = ""
date = "2016-04-19T22:10:39-03:00"
subtitle = ""
title = "Sling URL Resolve"

+++

When we are at the web, acessing pages and content, the URL is the definitive resource which inform what indeed
we're consuming.  
In the programming world, usually when we're using languages and technologies such as PHP, Ruby, NodeJS, the URL
points out to a resource, but in the Sling world, this is different.

## Content as the protagonist

As commented previously, in the Sling world, differently from the main technologies that we see out there, the content
is the main entry point of the application.  
When the framework is resolving the URL, it will try to match the content that is contained within the CRX Repository and,
just when the content is found, it will fetch it's properties in order to discover which script will be rendered.

The resolver will try to match the content in the URL by trying to find in the content repository by the full
path of the URL without the exception. If the content isn't found, it will then trigger the error handler script.

So, for example, the url ```http://localhost:4502/content/testing.html```, it try to find in the repository for
```/content/testing```.

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

### Script resolver

When the resolver is trying to find the script in one of the folders commented above,  it will take 3 things in 
consideration  in order to find the best script match.  
It will analyze those resources of the URL:

- The request method *(GET/HEAD/POST... etc...)*
- The selectors ```[URL]/testing.[selector1.selector2].html``` 
- The extension itself in the URL

In order to try to show how this is handled by AEM, I believe that the best way to do so is getting our hands dirty and
went straight to the code. So, in the next sections, let's see how to create the application environment need in order
to do so.

### Creating our application architecture

For us being able to assess this functionality of the Sling script resolver, we would need basically 3 things.

- A component that will be used to render our page content
- A template which our page will be based
- The page itself within our CRX content repository

So the first part is to create the structure of nodes that will hold our code. Following the Adobe best practices, we
should create our application folder within the ```/apps```. Let's call our application **blog1**.

So, in the first step, we should create folders using the **CRXDE Lite IDE** (that's bundled in every AEM installation)
in order to match the structure presented below.

    /apps
        /blog1
            /components
                /page
            /templates

As our simple application will only need a Page Component and a template, we don't need to create the full structure 
suggest by Adobe, which you can find in this [link](http://www.aemcq5tutorials.com/tutorials/adobe-aem-and-cq5-best-practices/).

So, first we need to enter in the LITE IDE by entering in the url ```http://localhost:4502/crx/de```. When the page is
loaded, you should be able to see something like this:
 
![CRXDE Lite](/posts/sling_url_resolve/crxde.png)
 
With that open, we should then start creating our folder structure. In order to create a folder in this application, 
you can click with right button in the ```/apps``` folder and click in create new folder, as in the screenshot down below.

![Creating a folder](/posts/sling_url_resolve/create_folder.png)

After all the folders are created, you should have a structure like this one:

![Folder structure](/posts/sling_url_resolve/folder_structure.png)

Now, with those folder totally ready, we can on to our next steps, which the first will be the creation of our component.

#### Creating our first component

Our component is the responsible by rendering what will be shown for our user, based in the content that is found by the
resolution of the URL. As commented previously, this component is found by the resolver by figuring out what is the
resource type of the content.  
In the Lite IDE, we have all we need in order to easily create a component, fact that we can accomplish by using the
create menu as we used before for the creation of our folder structure.

![Create component](/posts/sling_url_resolve/create_component.png)

When this button is clicked, it will be prompted for us a dialog area where we'll be able to fill the information which
describes our components. For now, the only info that we should fill is the Label (name of the node within the CRX repository)
and the Title. Let's call our component **slingtest**.

![Component config](/posts/sling_url_resolve/component_config.png)

For now, you should click in the next button in the dialog until the OK button is available, as it will not be needed to
configure the other details of the component for this tutorial, perhaps we can go deep in the component configuration in
another post.

With that, our component is successfully created and we can go on to the creation of our template.

#### Creating the template
 
The template is one major part of any AEM application, as it is what will drive the creation of all the content that 
will be presented for our clients. Any page is based in the templates in order to have it's default properties, such
as the resource type, that will define which component will render the resource content itself.  
In order to create a template, the process is similar to the creation of a component, we should just select the template
button instead.

![Create template](/posts/sling_url_resolve/create_template.png)

As we've already seen in the component creation, it will be presented a similar dialog where will be able to configure
our template. In this dialog, we will fill only 3 properties, the name and the title, as before, and also the 
**Resource Type**. This part is very important, because it's the part which will make our template tell all the pages based
on it, which component it should direct to render the content.  
I've commented previously that the Script resolver will try to find the component in two folders, the ```/apps``` and in the
```/libs```, so that's why in the Resource Type we just fill with ```blog1/components/page/slingtest```, because the
resolver will automatically concatenate the resource type to the default search folders.

![Configure Template: part 1](/posts/sling_url_resolve/configure_template_1.png)

Differently from the creation of components, in the next dialog there will be a field which will need our attention,
that is the **Allowed Paths**. This field is important because it defines where in the content repository tree our
page can be created. It accepts a regular exception, as we put ```/content(/.*)?```, where we said that we can create the
page anywhere in the tree that starts in the ```/content``` folder, that is the root directory of the all the sites
within AEM.

![Configure Template: part 2](/posts/sling_url_resolve/configure_template_2.png)

For the next steps, you can only skip clicking next until the OK button is available.

After this process is accomplished, we finally have our application created and now we can create our **first AEM page**
(congratz ;D).
 
 