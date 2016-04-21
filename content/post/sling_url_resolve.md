+++
bigimg = ""
date = "2016-04-19T22:10:39-03:00"
subtitle = ""
title = "Sling URL Resolve"

+++

When we are at the web, acessing pages and content, the URL is the definitive resource which inform what indeed
we're consuming.  
In the programming world, usually when we're using languages and technologies such as PHP, Ruby, NodeJS, the URL
points out to a script, but in the Sling world, this is different.

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
 
### Our window to the world: The first page

Now that we have built all the needed parts for our tests, we'll finally create our first page, but, in oder to do so, the first
thing that we should do is to access the *siteadmin* area, by entering this address ```http://localhost:4502/siteadmin```.  
When you enter in this part, you should be able to see a screen like this one:

![Siteadmin](/posts/sling_url_resolve/siteadmin.png)

In this area, you'll see all the created pages and also will be able to create new ones, basing it in the templates that
we've created previously.  
So, our first step is to create the page based in the template that we created. To do so, please click in the **New...**
button and choose **New Page...**, as in the screenshot.

![Siteadmin: New Page](/posts/sling_url_resolve/new_page.png)

When you finally clicked in this button, a dialog will be prompted for you where you will be able to enter the name of the 
node that will be within the CRX repository, the title of the page itself and select the template that it will be based.

![Siteadmin: New Page dialog](/posts/sling_url_resolve/creating_page.png)

In the case of the screenshot above, we will call our page Testing, naming it's node all lowercase. As you can see, as we're
at the root of the content, the ```/content``` location, as we created previously our template to be available for this 
path as well, it appears in the list of templates to be selected.  
After we created it and try to find the node of the Page within the CRX Repository, we will be able to notice that all
the properties defined in the template are also copied to the node of the page that was just created.

![Page properties](/posts/sling_url_resolve/page_properties.png)

### Resolver order

As commented before, there is some aspects of the URL that influence directly in the script resolver, and, to be true,
there is an order of preference that the resolver takes in consideration when trying to find the best match for the
resource content.  
In our component, we can, for example, have more than one **.jsp** script, so, by doing so, we will let our resolver be 
able to find the best match along a wide variety of options.  

So, in the first step for this, we should create some new **.jsp** files within our previously created component, so we
can then check for ourselves in practice what is the resolver order that AEM will take.  
We need to create some **files** in the following folder ```/apps/blog1/components/page/slingtest```. Use as example the
```slingtest.jsp``` that was created automatically when we created the component in the first place. We will click two times
in each file so we can change the content of the file to it's name. So for example, if we created the file ```test.jsp```,
in it's contents it would have ```test.jsp``` as well.

For this new part we should create this list of files.

- GET.jsp
- selector2.jsp
- selector1.jsp
- selector1/selector2.jsp

Don't forget to edit the contents of each file in order to it have as the content it's name. Take a look in the screenshoot
below.

![JSP Files](/posts/sling_url_resolve/jsp_files.png)

#### Testing our execution order

Now that we've created all the JSP files, the page and everything else, we can finally test in practice, when we access
the URL of our page, which indeed will be the scripted that will be rendered.  
So, let's try to access the page that we just created ```http://localhost:4502/content/testing.html```.

What was the page that rendered for you? Perhaps you're wondering why any of the page that we've created was called and
yes, the script that was created by default when the component was created at the first time, the ```slingtest.jsp```.

The reason behind that is the execution order, as there wasn't any specific file that match our URL, one of the last files
that the resolver tries to look for before triggering an error, is the jsp with the same name of the component, in this case
**slingtest**. If we deleted this file, there is just one more fallback before triggering an error, which is the 
**[HTTP_METHOD_NAME].jsp**. In our case ```GET.jsp```.  
If you try to delete the **slingtest.jsp** file, you will be able to see that the file that now will be called to render 
the content of the resource will be the ```GET.jsp```. Please, do so.

And if we deleted the ```GET.jsp``` as well, what would happen? In fact, there isn't any apparent error, we would just see
a blank screen. But now you know, that if anytime you experience just a blank screen, can be a problem of your script resolver
not finding anything to render.

##### Using the selectors

One good key point of AEM and Sling, is the possibility to tweak our request by adding selectors in our URL. Those selectors
have a priority when the script resolver is trying to find the script.  
For example, if we just had one selector in our URL, like this: ```http://localhost:4502/testing.selector2.html```, the 
script that would be called instead would be the ```selector2.jsp```.

And if we had more than one selector, like this URL: ```http://localhost:4502/testing.selector1.selector2.html```. What
would happen?  
Remember that we've created a jsp within a folder? In the execution order, if there is a folder with the name of the **first**
(and only the first) selector, it will enter in this folder. So you're right if you guessed that the one that would be called
was ```selector1/selector2.jsp```. You might be wondering that if we didn't had this folder, what would be the priority
of render, the selector1 or selector2? Let's try to delete the selector1 folder and discover.

If you guessed selector1, you were right, the first selector will always have the priority.

## Summing it up

Basically, that was the things that I've wanted to bring up for everyone, I really wish you could make usage of anything.  
If there is anything that I can help you out, please feel free to contact me via mail or any other social network! You can
find my contacts in the footer of this blog.

This is my first post, so sorry for any mistakes and please, warn me if there is anything that you think I could improve.  
Expect to see a lot of new content coming up, as I hope to use this also as a new way to learn, trying to write and explain
what I've been studying.

Thanks for holding up till here. ;)