# Introduction to Google Earth Engine
## The code editor 
This is the easiest way to run analysis and access the data available in GEE. The code editor is accessible through a web browser allowing us to write and execute scripts visualising the outputs almost instantaneously in the map view.

![the code editor][fig1]

[fig1]: ./figures/codeEditor.png

<div style="page-break-after: always;"></div>

## Searching for datasets, filtering and visualising images

<br />

>Objectives
>* Understand how to search for datasets using the code editor and import them into scripts.
>* Learn how to create a geometry and use it in a script.
>* Learn about image collections and how they can be filtered. 
>* Understand how the docs tab can be used to search for functions.
>* Understand visualisation parameters and use them to visualise an image in the map view. 

<br />

We can search for locations and available datasets that we want to use in our analysis using the search function. Interacting with data in the map view is achieved using the inspector tool. Clicking on the location in the map view prints this information to the console.

<br />

![inspector][fig2]

[fig2]:./figures/inspector.png

<div style="page-break-after: always;"></div>

><span style="color:red"> **Step 1:** Search for Auckland and use the inspector tool to find out the coordinates of a point roughly in central Auckland. Make a note of your coordinates for later by creating a geometry that is a point of your coordinates using the geometry tools.</span>

![createPoint][fig3]

[fig3]:./figures/createPoint.png

<br />

The inspector tool is very handy way of querying our data and extracting information that we can use in our scripts. The geometry tools are a quick easy way of generating a point, line or polygon that can be stored as an input to be used in our scripts. 

<br />

><span style="color:red"> **Step 2:** Lets search for a dataset. We'll use the Sentinel-2 level 2A collection. </span>

![imageCollection][fig4]

[fig4]:./figures/imageCollection.png

<br />

There are several ways we can access data in our scripts. In GEE a raster dataset is called an image collection. The simplest of these is using import. What happens if you click import?  

This imports the image collection under the variable ```imageCollection```  

 We can also copy the collection snippet to use in our code. 

<br />

><span style="color:red"> **Step 3:** Copy the collection snippet and paste it to the script window and edit the line to match to code below and hit run.  </span>

><span style="color:blue"> **Top-tip:** Typing your code rather than using copy & paste will help with your understanding of what each line is doing.</span>

```Javascript
var image_collection = ee.ImageCollection("COPERNICUS/S2_SR")
print(image_collection)
```

<br />

We now have the Sentinel-2 level 2A dataset stored under the variable ```image_collection```  

What happens when this is printed to the console?  

This collection contains all the level-2A data in the Sentinel-2 archive. We  can only print 5000 elements to the console in one print command so we get an error. We need to make our collection managable by filtering it. 

<br />


><span style="color:red"> **Step 4:** Filter the collection using our point we created earlier and the following code.</span>

```Javascript
// define sentinel-2 level 2A image collection
var image_collection = ee.ImageCollection("COPERNICUS/S2_SR")
    .filterBounds(geometry)

// print image_collection to console
print(image_collection)
```

<br />

What happens with our print command this time?  

We now get an ouput printed to console which contains a list of features showing us the number of elements/images in our collection that intersect with our geometry.  

Lets add a filter to obtain the least cloudy image in the collection so we can visualise it. 

<div style="page-break-after: always;"></div>

><span style="color:red"> **Step 5:** Add a metadata filter to your script to include images that have less than 5% cloud cover.</span>

```Javascript
// define sentinel-2 level 2A image collection 
var image_collection = ee.ImageCollection("COPERNICUS/S2_SR")
    .filterBounds(geometry)
    .filterMetadata('CLOUDY_PIXEL_PERCENTAGE', "less_than", 5)

// print image_collection to console
print(image_collection)
```

<br />

What does this do to the number of elements in our collection?  

We can use the metadata filter to filter a collection by any of ther properties in the metadata. Now we can select an image from our collection to visualise. There are several other useful filters that we can use. We can search for these in the docs tab.

<br />

><span style="color:red"> **Step 6:** Return the first image in the collection and print it to the console.</span>

```Javascript
// define sentinel-2 level 2A image collection 
var image_collection = ee.ImageCollection("COPERNICUS/S2_SR")
    .filterBounds(geometry)
    .filterMetadata('CLOUD_COVER', "less_than", 5)

// print image_collection to console
print(image_collection)

// define the first image in image_collection
var sentinel_image = image_collection.first()

// print sentinel_image to console
print(sentinel_image)
```
<br />

How does the output from the final print command differ from first one? We now have a single image which contains a list of 23 elements. These are the image bands in the raster. We are now ready to visualise the image in the map view. 

<div style="page-break-after: always;"></div>

><span style="color:red"> **Step 7:** Define some visualisation parameters and add the image to the map view.</span>

```Javascript
// return the first image in image_collection
var sentinel_image = image_collection.first()

// print sentinel_image to console
print(sentinel_image)

// define visualisation parameters for sentinel_image
var visParams = {min: 0, max: 3000, bands:['B4', 'B3', 'B2']}

// add sentinel_image to map view
Map.addLayer(sentinel_image, visParams, 'sentinel-2 image')
```

Once we run the script we should see the image in the map view. Well done! You've visualised your first satellite image in GEE. The visulisation parameters are stored as a dictionary and can be defined as a variable or included in ```addLayer()``` function (e.g. ```Map.addLayer(sentinel_image, {min: 0, max: 3000, bands:['B4', 'B3', 'B2']}, 'sentinel-2 image')```).  

The ```min``` and ```max``` value are the pixels values used to apply the stretch and the ```bands``` define the image bands to be used in the Red, green, and blue colour channels respectively. In this case ```['B4', 'B3', 'B2']``` refer to the RGB bands for Sentinel-2 resulting in a true colour image.  

<div style="page-break-after: always;"></div>

<blockquote style=color:red>
<p><b>A few more things to think about:</b>
<ul>
<li>What would you change in <code>visParams</code> to visualise your image as a false colour composite where the Red channel represents the near-infrared band?</li>
<li>What happens if you select the inspector tool and click on a pixel in your image?</li>
<li>How would you filter your image collection to return images in a given date range?</li>
</p>
</blockquote> 

<br />

<blockquote style=color:black;background-color:#96CF98>
<p><b>Key points</b>
<ul>
<li>An image collection contains all available data for a given sensor and this can be imported into a script or referenced using it's code snippet.</li>
<li>The search bar can be used to search for locations and any data available in GEE.</li>
<li>We use the filtering functions to obtain data for a given area, date range or filter a collection by metadata properties.</li>
<li>We must define visualisation parameters to view an image in the map view.</li>
<li>We can query information/data in the map view using the insepctor tool.</li>
<li>We can create lines, polygons or point geometries as imports to use in scripts.</li>
</p>
</blockquote>