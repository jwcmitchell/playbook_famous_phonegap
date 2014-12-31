# Models / Collections


We are using only a few libraries on top of `Backbone.Model` and `Backbone.Collection`.  

Goals for our additions to models/collections are: 
- deep model support (mongodb) 
- cache locally, ideally store to file system (not working)
- minimize overhead in responses (_id and __v) 
- deliver events across equivalent model/collection instance (based on _id or url query) 
- easier fire once-and-only-once call for model/collection
- infinite list pagination (sorting, caching)
- easy data-binding server->client (two-way not important)
- better add/edit model transforms (profile_name -> profile.name) 
- offline support, corresponding Surface fallbacks



### populated() 

The most glaring addition to syntax is the addition of a `populated` function for models and collection. This function returns a promise. By writing the following:
    
    var ItemModel = require('models/item');
    var MyItem = ItemModel.Item({
        _id: "5491dac1391f470000886e47"
    })
    MyItem.populated().then(function(){
        alert(1);
    });
    MyItem.fetch();
    
we guarantee that `alert(1)` will be fired __once and only once__, when the first "fetch" command has succeeded (including if it was fetched from our cache). 

The same holds true for a collection: 

    var ItemModel = require('models/item');
    var MyItemCollection = ItemModel.ItemCollection();
    MyItemCollection.populated().then(function(){
        alert(1);
    });
    MyItemCollection.fetch();

Even if the model or collection were fetched __prior to the creation of the `populated()`__ call, our promise will be resolved once:

    var ItemModel = require('models/item');
    var MyItemCollection = ItemModel.ItemCollection();
    MyItemCollection.fetch();
    
    Timer.setTimeout(function(){
        MyItemCollection.populated().then(function(){
            // fires 10 seconds after the model has actually been populated
            alert(1);
        });
    },10 * 1000);
    




