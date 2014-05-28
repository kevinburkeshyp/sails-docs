# Attributes
### Overview

Model attributes are basic pieces of information about a model. A model called `Person` might have attributes called `firstName`, `lastName`, `phoneNumber`, `age`, `birthDate` and `emailAddress`.

> TODO: address sql vs. no sql and stuff like:
> """
> In most cases, this data is _homogenous_, meaning each record has the same attributes, 
> """

### Attribute Options

These options can be used to enforce various constraints and add special enhancements to our model attributes. 

###### type

Specifies the type of data that will be stored in this attribute.  One of:

- string
- text
- int
- float
- date
- time
- datetime
- boolean
- binary
- array
- json


###### defaultsTo

When a record is created, if no value was supplied, the record will be created with the specified `defaultsTo` value.

```javascript
attributes: {
  phoneNumber: {
    type: 'string',
    defaultsTo: '111-222-3333'
  }
}
```

###### autoIncrement

Sets up the attribute as an auto-increment key.  When a new record is added to the model, if a value for this attribute is not specified, it will be generated by incrementing the most recent record's value by one. Note: Attributes which specify `autoIncrement` should always be of `type: integer`. Also, bear in mind that the level of support varies across different datastores. For instance, MySQL will not allow more than one auto-incrementing column per table.

```javascript
attributes: {
  placeInLine: {
    type: 'integer',
    autoIncrement: true
  }
}
```

###### unique

Ensures no two records will be allowed with the same value for the target attribute. This is an adapter-level constraint, so in most cases this will result in a unique index on the attribute being created in the underlying datastore.

```javascript
attributes: {
  username: {
    type: 'string',
    unique: true
  }
}
```

<!--

Omitting `index` from docs for now.

###### index

Will create a simple index in the underlying datastore for faster queries if available. This is only for simple indexes and currently dosn't support compound indexes. For these you will need to create them yourself or use a migration.

There is currently an issue with adding indexes to string fields. Because Waterline performs its queries in a case insensitive manner we are unable to use the index on a string attribute. There are some workarounds being discussed but nothing is implemented so far. This will be updated in the near future to fully support indexes on strings.

```javascript
attributes: {
  email: {
    type: 'string',
    index: true
  }
}
```
-->

###### primaryKey

Use this attribute as the the primary key for the record. Only one attribute per model can be the `primaryKey`.  Note: This should never be used unless [autoPK]() is set to false.

```javascript
attributes: {
  uuid: {
    type: 'string',
    primaryKey: true,
    required: true
  }
}
```

###### enum

A special validation property which only saves data which matches a whitelisted set of values.

```javascript
attributes: {
  state: {
    type: 'string',
    enum: ['pending', 'approved', 'denied']
  }
}
```

<!--
These are not ready for prime-time yet, but listing them here so they're easy to reference and add to official docs later:

###### example

An example value for this attribute, e.g. "Albus Dumbledore".


###### validationMessage

A custom validation message to use when any validations fail for this attribute.

-->

###### size

If supported in the adapter, can be used to define the size of the attribute. For example in MySQL, `size` can be specified as a number (`n`) to create a column with the SQL data type: `varchar(n)`.

```javascript
attributes: {
  name: {
    type: 'string',
    size: 24
  }
}
```

###### columnName


Inside an attribute definition, you can specify a `columnName` to force Sails/Waterline to store data for that attribute in a specific column in the configured connection (i.e. database).  Be aware that this is not necessarily SQL-specific-- it will also work for MongoDB fields, etc.

While the `columnName` property is primarily designed for working with existing/legacy databases, it can also be useful in situations where your database is being shared by other applications, or you don't have access permissions to change the schema.

To store/fetch your model's `numberOfWheels` attribute into/from the `number_of_round_rotating_things` column:
```javascript
  // An attribute in one of your models:
  // ...
  numberOfWheels: {
    type: 'integer',
    columnName: 'number_of_round_rotating_things'
  }
  // ...
```


Now for a more thorough/realistic example.

Let's say you have a `User` model in your Sails app that looks like this:

```javascript
// api/models/User.js
module.exports = {
  connection: 'shinyNewMySQLDatabase',
  attributes: {
    name: 'string',
    password: 'string',
    email: {
      type: 'email',
      unique: true
    }
  }
};
```


Everything works great, but instead of using an existing MySQL database sitting on a server somewhere that happens to house your app's intended users:

```javascript
// config/connections.js
module.exports = {
  // ...
  
  // Existing users are in here!
  rustyOldMySQLDatabase: {
    adapter: 'sails-mysql',
    user: 'bofh',
    host: 'db.eleven.sameness.foo',
    password: 'Gh19R!?had9gzQ#Q#Q#%AdsghaDABAMR>##G<ADMBOVRH@)$(HTOADG!GNADSGADSGNBI@(',
    database: 'jonas'
  },
  // ...
};
```

Let's say there's a table called `our_users` in the old MySQL database that looks like this:

| the_primary_key | email_address | full_name | seriously_encrypted_password|
|------|---|----|---|
| 7 | mike@sameness.foo | Mike McNeil | ranchdressing |
| 14 | nick@sameness.foo | Nick Crumrine | thousandisland |


In order to use this from Sails, you'd change your `User` model to look like this:

```javascript
// api/models/User.js
module.exports = {
  connection: 'rustyOldMySQLDatabase',
  tableName: 'our_users',
  attributes: {
    id: {
      type: 'integer',
      unique: true,
      primaryKey: true,
      columnName: 'the_primary_key'
    },
    name: {
      type: 'string',
      columnName: 'full_name'
    },
    password: {
      type: 'string',
      columnName: 'seriously_encrypted_password'
    },
    email: {
      type: 'email',
      unique: true,
      columnName: 'email_address'
    }
  }
};
```

> You might have noticed that we also used the [`tableName`]() property in this example.  This allows us to control the name of the table that will be used to house our data.







<docmeta name="uniqueID" value="Attributes951609">
<docmeta name="displayName" value="Attributes">
