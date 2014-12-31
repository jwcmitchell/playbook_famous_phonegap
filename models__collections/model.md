# Model



An except from `models/action.js`:

    var Action = Backbone.DeepModel.extend({

        idAttribute: '_id',

        urlRoot: Credentials.server_root + "action/",

        initialize: function () {
            this.url = this.urlRoot + '';
            if(this.id){
                this.url = this.urlRoot + '/' + this.id;
            }
        }

    });

    Action = Backbone.UniqueModel(Action);


#### Backbone.DeepModel

Allows us to query nested attributes in a model. We can do:

    ourModel.get('data.nested.attr');

instead of repetitive code like:

    if(ourModel.get('data') && ourModel.get('data').nested && ourModel.get('data').nested.attr){...}




#### Backbone.UniqueModel

Gives us an App-wide version of *the same* model everytime we instantiate one based on `_id`.

> But, __EVENTS__ are not synced! (at least, 'change' is not) (todo: fix) 

    var ActionModel = require('models/action');
    var a = ActionModel.Action({
        _id: "5491dac1391f470000886e48" // ObjectId
    });
    a.on('question', function({
        console.log("answer for a");
    });
    var b = ActionModel.Action({
        _id: "5491dac1391f470000886e48", // ObjectId
    });

    > a === b
    true

    > b.trigger('question');
    "answer for a"








