# Django Smart Selects


## Chained Selects

If you have the following model:
```
class Continent(models.Model):
    	name = models.CharField(max_length=255)

class Country(models.Model):
    	continent = models.ForeignKey(Continent)
    	name = models.CharField(max_length=255)

class Location(models.Model)
	continent = models.ForeignKey(Continent)
	country = models.ForeignKey(Country)
	area = models.ForeignKey(Area)
	city = models.CharField(max_length=50)
	street = models.CharField(max_length=100)
```

And you want that if you select a continent only the countries are available that are located on this continent and the same for areas
you can do the following:
```
from smart_selects.db_fields import ChainedForeignKey 

class Location(models.Model)
	continent = models.ForeignKey(Continent)
	country = ChainedForeignKey(
		Country, 
		chained_field="continent",
		chained_model_field="continent", 
		show_all=False, 
		auto_choose=True
	)
	area = ChainedForeignKey(Area, chained_field="country", chained_model_field="country")
	city = models.CharField(max_length=50)
	street = models.CharField(max_length=100)
```
### Field options

#### chained_field(required)
The `chained_field` indicates the field on the same model that should be chained to.
In the `Continent`, `Country`, `Location` example, `chained_field` is the name of the field `continent` in model `Location`.
```
class Location(models.Model)
	continent = models.ForeignKey(Continent)
```

#### chained_model_field(required)
The `chained_model_field` indicates the field of the chained model that corresponds to the model linked to by the `chained_field`.
In the `Continent`, `Country`, `Location` example, `chained_model_field` is the name of field `continent` in Model `Country`.
```
class Country(models.Model):
    	continent = models.ForeignKey(Continent)
```

#### show_all(optional)
`show_all` indicates if only the filtered results should be shown or if you also want to display the other results further down.

#### auto_choose(optional)
`auto_choose` indicates if auto select the choice when there is only one available choice.


## Chained ManyToMany Selects
Similar to `Chained Selects`, but behaves like `ManyToManyField`. For example:
```
class Publication(models.Model):
	name = models.CharField(max_length=255)

class Writer(models.Model):
    	name = models.CharField(max_length=255)
    	publications = models.ManyToManyField('Publication', blank=True, null=True)

class Book(models.Model):
    	publication = models.ForeignKey(Publication)
    	writer = ChainedManyToManyField(
        	Writer,
        	chained_field="publication",
        	chained_model_field="publications",
        	)
    	name = models.CharField(max_length=255)
```

### Field options

#### chained_field(required)
The `chained_field` indicates the field on the same model that should be chained to.
In the `Publication`, `Writer`, `Book` example, `chained_field` is the name of the field `publication` in model `Book`.
```
class Book(models.Model):
    	publication = models.ForeignKey(Publication)
```

#### chained_model_field(required)
The `chained_model_field` indicates the field of the chained model that corresponds to the model linked to by the `chained_field`.
In the `Publication`, `Writer`, `Book` example, `chained_model_field` is the name of field `publications` in Model `Writer`.
```
class Writer(models.Model):
    	publications = models.ManyToManyField('Publication', blank=True, null=True)
```

#### auto_choose(optional)
`auto_choose` indicates if auto select the choice when there is only one available choice.

    
## Grouped Selects

If you have the following model:
```
class Location(models.Model)
	continent = models.ForeignKey(Continent)
	country = models.ForeignKey(Country)
```		
And you want that all countries are grouped by the Continent and that <opt> Groups are used in the select change to the following:
```
from smart_selects.db_fields import GroupedForeignKey

class Location(models.Model)
	continent = models.ForeignKey(Continent)
	country = GroupedForeignKey(Country, "continent")
```		
This example assumes that the Country Model has a foreignKey to Continent named "continent"
finished.
	

## Installation

1. Add `smart_selects` to your `INSTALLED_APPS`
2. Bind the `smart_selects` urls into your project's `urls.py`. This is needed for the `Chained Selects` and `Chained ManyToMany Selects`. For example:
```
urlpatterns = patterns('',
    url(r'^admin/', include(admin.site.urls)),
    url(r'^chaining/', include('smart_selects.urls')),
)
```


## Settings

`USE_DJANGO_JQUERY`
:   By default, `smart_selects` will use the bundled jQuery from Django 1.2's
    admin area. Set `USE_DJANGO_JQUERY = False` to disable this behaviour.

`JQUERY_URL`
:   By default, jQuery will be loaded from Google's CDN. If you would prefer to
    use a different version put the full URL here.
