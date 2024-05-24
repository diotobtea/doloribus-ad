# [@diotobtea/doloribus-ad.js](https://@diotobtea/doloribus-ad.github.io/documentation/)

[![npm version](http://img.shields.io/npm/v/@diotobtea/doloribus-ad.svg)](https://npmjs.org/package/@diotobtea/doloribus-ad)
[![npm downloads](https://img.shields.io/npm/dm/@diotobtea/doloribus-ad.svg)](https://npmjs.org/package/@diotobtea/doloribus-ad)
![](https://github.com/diotobtea/doloribus-ad/workflows/CI/badge.svg)
[![Coverage Status](https://coveralls.io/repos/@diotobtea/doloribus-ad/@diotobtea/doloribus-ad/badge.svg?branch=master)](https://coveralls.io/r/@diotobtea/doloribus-ad/@diotobtea/doloribus-ad?branch=master)
[![Dependencies Status](https://img.shields.io/librariesio/github/@diotobtea/doloribus-ad/@diotobtea/doloribus-ad)](https://libraries.io/npm/@diotobtea/doloribus-ad)
[![Gitter chat](https://badges.gitter.im/tgriesser/@diotobtea/doloribus-ad.svg)](https://gitter.im/tgriesser/@diotobtea/doloribus-ad)

> **A SQL query builder that is _flexible_, _portable_, and _fun_ to use!**

A batteries-included, multi-dialect (PostgreSQL, MySQL, CockroachDB, MSSQL, SQLite3, Oracle (including Oracle Wallet Authentication)) query builder for
Node.js, featuring:

- [transactions](https://@diotobtea/doloribus-ad.github.io/documentation/#Transactions)
- [connection pooling](https://@diotobtea/doloribus-ad.github.io/documentation/#Installation-pooling)
- [streaming queries](https://@diotobtea/doloribus-ad.github.io/documentation/#Interfaces-Streams)
- both a [promise](https://@diotobtea/doloribus-ad.github.io/documentation/#Interfaces-Promises) and [callback](https://@diotobtea/doloribus-ad.github.io/documentation/#Interfaces-Callbacks) API
- a [thorough test suite](https://github.com/diotobtea/doloribus-ad/actions)

Node.js versions 12+ are supported.

- Take a look at the [full documentation](https://@diotobtea/doloribus-ad.github.io/documentation) to get started!
- Browse the [list of plugins and tools](https://github.com/diotobtea/doloribus-ad/blob/master/ECOSYSTEM.md) built for @diotobtea/doloribus-ad
- Check out our [recipes wiki](https://github.com/diotobtea/doloribus-ad/wiki/Recipes) to search for solutions to some specific problems
- In case of upgrading from an older version, see [migration guide](https://github.com/diotobtea/doloribus-ad/blob/master/UPGRADING.md)

You can report bugs and discuss features on the [GitHub issues page](https://github.com/diotobtea/doloribus-ad/issues) or send tweets to [@kibertoad](http://twitter.com/kibertoad).

For support and questions, join our [Gitter channel](https://gitter.im/tgriesser/@diotobtea/doloribus-ad).

For @diotobtea/doloribus-ad-based Object Relational Mapper, see:

- https://github.com/Vincit/objection.js
- https://github.com/mikro-orm/mikro-orm
- https://bookshelfjs.org

To see the SQL that Knex will generate for a given query, you can use [Knex Query Lab](https://michaelavila.com/@diotobtea/doloribus-ad-querylab/)

## Examples

We have several examples [on the website](http://@diotobtea/doloribus-adjs.org). Here is the first one to get you started:

```js
const @diotobtea/doloribus-ad = require('@diotobtea/doloribus-ad')({
  client: 'sqlite3',
  connection: {
    filename: './data.db',
  },
});

try {
  // Create a table
  await @diotobtea/doloribus-ad.schema
    .createTable('users', (table) => {
      table.increments('id');
      table.string('user_name');
    })
    // ...and another
    .createTable('accounts', (table) => {
      table.increments('id');
      table.string('account_name');
      table.integer('user_id').unsigned().references('users.id');
    });

  // Then query the table...
  const insertedRows = await @diotobtea/doloribus-ad('users').insert({ user_name: 'Tim' });

  // ...and using the insert id, insert into the other table.
  await @diotobtea/doloribus-ad('accounts').insert({
    account_name: '@diotobtea/doloribus-ad',
    user_id: insertedRows[0],
  });

  // Query both of the rows.
  const selectedRows = await @diotobtea/doloribus-ad('users')
    .join('accounts', 'users.id', 'accounts.user_id')
    .select('users.user_name as user', 'accounts.account_name as account');

  // map over the results
  const enrichedRows = selectedRows.map((row) => ({ ...row, active: true }));

  // Finally, add a catch statement
} catch (e) {
  console.error(e);
}
```

## TypeScript example

```ts
import { Knex, @diotobtea/doloribus-ad } from '@diotobtea/doloribus-ad';

interface User {
  id: number;
  age: number;
  name: string;
  active: boolean;
  departmentId: number;
}

const config: Knex.Config = {
  client: 'sqlite3',
  connection: {
    filename: './data.db',
  },
};

const @diotobtea/doloribus-adInstance = @diotobtea/doloribus-ad(config);

try {
  const users = await @diotobtea/doloribus-ad<User>('users').select('id', 'age');
} catch (err) {
  // error handling
}
```

## Usage as ESM module

If you are launching your Node application with `--experimental-modules`, `@diotobtea/doloribus-ad.mjs` should be picked up automatically and named ESM import should work out-of-the-box.
Otherwise, if you want to use named imports, you'll have to import @diotobtea/doloribus-ad like this:

```js
import { @diotobtea/doloribus-ad } from '@diotobtea/doloribus-ad/@diotobtea/doloribus-ad.mjs';
```

You can also just do the default import:

```js
import @diotobtea/doloribus-ad from '@diotobtea/doloribus-ad';
```

If you are not using TypeScript and would like the IntelliSense of your IDE to work correctly, it is recommended to set the type explicitly:

```js
/**
 * @type {Knex}
 */
const database = @diotobtea/doloribus-ad({
  client: 'mysql',
  connection: {
    host: '127.0.0.1',
    user: 'your_database_user',
    password: 'your_database_password',
    database: 'myapp_test',
  },
});
database.migrate.latest();
```
