# [Lithium PHP](http://lithify.me) Plugin to allow relations on a Model basis

Now, don't get too extatic, this is in its early stages and only supports READ opperations & hasMany or hasOne. Hope to add support for full CRUD in the future.

Simply, this is a plugin designed to add support to li3 for relations on a Model to Model basis seperate from connections. This allows not only relationship support for non supportted DBs, such as MongoDB, but also allows relationships to be cross connection/data sources. Such as Solr backed model to a Mongo backed Model. For the SQL lovers, the plugin checks to see if the current model and related model both are of the same source and support relations. (aka two MySQL backed models will continue to do a single query with a Left Join). When these same source relations are not avaible, the relationship will do a batch query to the targetted model.

## Installation

### Use Composer
Modify your projects `composer.json` file

~~~ json
{
    "require": {
    	...
        "brandonwestcott/li3_relations": "master"
        ...
    }
}
~~~

Run `./composer.phar install` which will install this librarie into your app/libraries

### Alternately, just clone, download or submodule
1. Clone/Download/submodule the plugin into your app's ``libraries`` directory.
2. Tell your app to load the plugin by adding the following to your app's ``config/bootstrap/libraries.php``:

## Usage

Add the plugin in your `config/bootstrap/libraries.php` file:

~~~ php
<?php
	Libraries::add('li3_relations');
?>
~~~

Then in any model, extend \li3_relations\extensions\data\Model.php
~~~ php
namespace app\models;

class AppModel extends \app\extensions\data\Model {

~~~

#### Defining Relations

Relations are defined in the lithium specified way as described [here](http://lithify.me/docs/manual/working-with-data/relationships.wiki)

In hasMany

~~~ php
class Team extends  \li3_relations\extensions\data\Model.php {

	public $hasMany = array(
		'Players' => array(
			'to'        => 'Players',
			'key'       => array('player_ids' => '_id'),
			'fieldName' => 'players',
			'with' 		=> array(
				'Agent'
			),
 		),
		'Coaches' => array(
			'to'     => 'Coaches',
			'key'       => array('coach_ids' => '_id'),
			'fieldName' => 'coaches',
		),
	);

	public $hasOne = array(
		'HeadCoach' => array(
			'to'     => 'Coaches',
			'key'       => array('head_coach_id' => '_id'),
			'fieldName' => 'head_coach',
		),
	);

~~~

Key specified is the name used to refernce the relation on a find query.

Options are:
to     		- specifieds target model
fieldName   - field to create relation on - defaults to camelCased Name
fields 		- fields to pass to query
order  		- order to pass to query
conditions  - conditions to pass to query
with 		- relations for the relation

#### Calling Relations

Relations are called in the lithium specified way as described [here](http://lithify.me/docs/manual/working-with-data/relationships.wiki)

~~~ php
Team::find('first', array(
	'_id' => 1,
	'with' => array(
		'Players',
		'HeadCoach',
		'Coaches',
	),
));
~~~

In the case of using MongoDB, this would return a Document of a Team. On the team would exists 3 additional properties, players, coaches, and head_coaches. (Debating to make the reference live in a magic method vs a property - any input is welcome)

hasOnes will be set as type Entity as specified by their source (MongoDB creates Document, SQL creates Record).

hasManies will be set as type Set as specified by their source (MongoDB creates DocumentSet, SQL creates RecordSet).

However, when no data is returned, the behavior is slightly different. An empty hasOne will return null, as is the behavior of calling Model::find('first'). An empty hasMany will continue to return an empty Set, as is the behavior of calling Model::find('all').

Notice here that each of the Players on the Team would also include and Agent on that Document, which would be a relationship specified inside the Players model.


## Some Notes
1. Beta Beta Beta - Currently, this plugin is being used heavily in a read MongoDB & Solr production environment. However, writes will likely majorly screw up your db. Use with caution.

## Plans for the future
Need to get full CRUD on these relationships.

## Collaborate
Please fork and contribute!