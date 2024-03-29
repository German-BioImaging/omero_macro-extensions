[![Java CI with Maven](https://github.com/GReD-Clermont/omero_macro-extensions/actions/workflows/maven.yml/badge.svg)](https://github.com/GReD-Clermont/omero_macro-extensions/actions/workflows/maven.yml)
[![DOI](https://img.shields.io/badge/DOI-10.12688%2Ff1000research.110385.2-GREEN)](https://doi.org/10.12688/f1000research.110385.2)

# OMERO Macro Extensions

A plugin for ImageJ to provide macro extensions to access OMERO.

## How to install

1. Install the [OMERO.insight plugin](https://omero-guides.readthedocs.io/en/latest/fiji/docs/installation.html) (if you
   haven't already).
2. Download the JAR file for this [library](https://github.com/GReD-Clermont/simple-omero-client/releases/tag/5.16.0/).
3. Download the JAR file ([for this plugin](https://github.com/GReD-Clermont/omero_macro-extensions/releases/tag/1.3.3/)).
4. Place these JAR files in your plugins folder.

## How to use

Extensions first have to be loaded:

```
run("OMERO Extensions");
```

### Connection:

Connecting to OMERO is done using:

```
Ext.connectToOMERO("host", 4064, "username", "password");
```

Then, switching group can be performed through:

```
Ext.switchGroup(groupId);
```

You can also choose to only list objects for a given user (or all if username is empty or equal to "all"):

```
Ext.listForUser(username);
```

When done, you can disconnect using:

```
Ext.disconnect()
```

### Listing repository objects (projects, datasets, images, tags)

Once connected, projects, datasets, images and tags IDs can be listed, and their name retrieved:

* Projects:

```
projects = Ext.list("projects");
print(projects);
projectIds = split(projects,",");
projectName = Ext.getName("project", projectIds[0]);
print(projectName);
```

* Datasets:

```
datasets = Ext.list("datasets");
print(datasets);
datasetIds = split(datasets,",");
datasetName = Ext.getName("dataset", datasetIds[0]);
print(datasetName);
```

* Images:

```
images = Ext.list("images");
print(images);
imageIds = split(images,",");
imageName = Ext.getName("image", imageIds[0]);
print(imageName);
```

* Screens:

```
screens = Ext.list("screens");
print(screens);
screenIds = split(screens,",");
screenName = Ext.getName("screen", screenIds[0]);
print(screenName);
```

* Plates:

```
plates = Ext.list("plates");
print(plates);
plateIds = split(plates,",");
plateName = Ext.getName("plate", plateIds[0]);
print(plateName);
```

* Wells:

```
wells = Ext.list("wells");
print(wells);
wellIds = split(wells,",");
wellName = Ext.getName("well", wellIds[0]);
print(wellName);
```

* Tags:

```
tags = Ext.list("tags");
print(tags);
tagIds = split(tags,",");
tagName = Ext.getName("tag", tagIds[0]);
print(tagName);
```

### Listing objects with a given name

It is also possible to list objects with a specific name:

* Projects:

```
projects = Ext.list("projects", "name");
```

* Datasets:

```
datasets = Ext.list("datasets", "name");
```

* Images:

```
images = Ext.list("images", "name");
```

* Screens:

```
screens = Ext.list("screens", "name");
```

* Plates:

```
plates = Ext.list("plates", "name");
```

* Tags:

```
tags = Ext.list("tags", "name");
```

### Listing objects inside a given repository object

Each object ID can then be used to retrieve the contained objects (IDs and names).

It is, for example, possible to list datasets inside a project:

```
datasets = Ext.list("datasets", "project", projectIds[0]);
print(datasets);
datasetIds = split(datasets,",");
datasetName = Ext.getName("dataset", datasetIds[0]);
print(datasetName);
```

It is also possible to list tags attached to an image:

```
tags = Ext.list("tags", "image", imageIds[0]);
print(tags);
tagIds = split(tags,",");
tagName = Ext.getName("tag", tagIds[0]);
print(tagName);
```

Similarly, a dataset ID can be used to retrieve its images:

```
images = Ext.list("images", "dataset", datasetIds[0]);
print(images);
imageIds = split(images,",");
imageName = Ext.getName("image", imageIds[0]);
print(imageName);
```

Screens, plates and wells can also be browsed this way:

```
screens = Ext.list("screens");
screenIds = split(screens,",");
screenName = Ext.getName("screen", screenIds[0]);
plates = Ext.list("plates", "screen", screenIds[0]);
plateIds = split(plates,",");
wells = Ext.list("wells", "plate", plateIds[0]);
wellIds = split(wells,",");
images = Ext.list("images", "well", wellIds[0]);
print(images);
imageIds = split(images,",");
imageName = Ext.getName("image", imageIds[0]);
print(imageName);
```

Finally, intermediate hierarchy levels can be skipped when listing contained objects:

```
images = Ext.list("images", "project", projectIds[0]);
print(images);
imageIds = split(images,",");
imageName = Ext.getName("image", imageIds[0]);
print(imageName);
```

### Creating projects, datasets and tags

Projects can be created with *Ext.createProject*:

```
projectId = Ext.createProject(name, description);
```

Datasets can be created with *Ext.createDataset*:

```
datasetId = Ext.createDataset(name, description, projectId);
```

Tags can be created with *Ext.createTag*:

```
tagId = Ext.createTag(name, description);
```

### Linking/unlinking objects

Objects can be linked with *Ext.link*, e.g.:

```
Ext.link("dataset", datasetId, "tag", tagId);
```

They can also be unlinked with *Ext.unlink*, e.g.:

```
Ext.unlink("dataset", datasetId, "tag", tagId);
```

### Deleting objects

Objects can be deleted with *Ext.delete*:

```
Ext.delete("project", projectId);
```

### Opening images

Pixel intensities can be retrieved from images:

```
imageplusID = Ext.getImage(imageIds[0]);
```

Images can also be cropped on import by specifying the ROI as a String of the form:
"x:xstart:xend,y:ystart:yend,c:cstart:cend,z:zstart:zend,t:tstart:tend".
For example, cropping a 512x512x3x100x5 image starting at (0,0,0,50,3) and ending at (100,100,2,99,3) can be done with:

```
imageplusID = Ext.getImage(imageIds[0], "x:0:100,y::100,z:50:,t:3");
```

ROIs from OMERO can also be added to the ROI manager or to the Overlay of the current image (boolean toOverlay). ROIs
composed of multiple shapes (eg 3D/4D) will share the same values in the "ROI" and "ROI_ID" properties in ImageJ. These
can be optionally changed with the "property" parameter: local indices will be in "property" while OMERO IDs will be
in "property + _ID". This is achieved through:

```
nIJROIs = Ext.getROIs(imageIds[0], toOverlay, property);
```

Conversely, ImageJ ROIs can also be saved to OMERO (the property is used to group ImageJ shapes into a single 3D/4D ROI
on OMERO, if the string is empty, "ROI" is used):

```
nROIS = Ext.saveROIs(imageId, property);
```

### Saving images

The current image can be saved (as a TIF) to a dataset in OMERO:

```
newImageId = Ext.importImage(datasetId);
```

### Attaching / deleting files

Files can be attached to a project/dataset/image through *Ext.addFile*:

```
fileId = Ext.addFile('image', imageId, path);
```

They can also be deleted with *Ext.deleteFile*:

```
Ext.deleteFile(fileId);
```

### Attaching a table

A table can be created/updated using the results with *Ext.addToTable*:

```
Ext.addToTable(tableName, resultsName, imageId, roiProperty);
```

If a column named ROI containing ROI IDs is present, these will be added to the table. Alternatively, if ROIs that were
saved to OMERO are in the ROI Manager and if their name appears in the labels or is in a column named ROI, they will be
added too.

The table can then be saved to a project/dataset/image through *Ext.saveTable*:

```
Ext.saveTable(tableName, 'dataset', datasetId);
```

It can then be saved to a delimited text file through *Ext.saveTableAsFile* (default separator is ','):

```
Ext.saveTableAsFile(tableName, pathToFile, delimiter);
```

### Work as another user (sudo)

If a user has sudo rights, it is possible to do all the above as another user:

```
Ext.sudo(otherUsername);
```

To switch back to the original user, this command should be used:

```
Ext.endSudo();
```

## License

[GPLv2+](https://choosealicense.com/licenses/gpl-2.0/)
