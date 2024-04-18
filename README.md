# Refactoring the Fellow Tracker

## Part 1 - Adding a DB for the fellows

1. create a database called `fellows_tracker_f23`
2. Create the table structure

    ```sql
    CREATE TABLE fellows (
      id SERIAL PRIMARY KEY,
      name TEXT NOT NULL
    )
    ```

3. `cd` into `server/`
4. `npm i pg knex`
5. `npx knex init` to create `knexfile.js` in the root of the `server/` directory
6. modify the file to use `pg` and the `fellow-tracker-f23` database with a valid username and password
7. create a `knex.js` file in the `model/` directory
   - make sure the import statement for `knexfile.js` is accurate

    ```js
    // Set the deployment environment variable
    // Depending on where it is deployed, this could be "staging" or "production"
    const env = process.env.NODE_ENV || 'development';

    // Grab the corresponding knex configuration object from knexfile.js
    const knexConfig = require('../knexfile.js')[env];

    // Create the knex connection object using that config
    const knex = require('knex')(knexConfig);

    module.exports = knex;
    ```

8.  Refactor `Fellow.js`!
    1. Import `knex` into the `Fellow` model
    2. Remove the import of the `getId` function
    3. Remove the `Fellow.#all` array
    4. Remove the `constructor` entirely and create a `static async create` method (we'll use the constructor for another purpose)
    5. All functions need to be `async` to use `await knex.raw()`
    6. Every function needs to return *something* (use `RETURNING *`)
       1. Create should return the new fellow
       2. Get ALL should return an array of all fellows
       3. Get One should return a single fellow object (the first object in the `rows` array)
       4. Update should return the updated fellow object
       5. Delete should return the deleted fellow object
9.  Refactor `fellowControllers.js` to use the new model methods and to `await` everything


## Part 2 - Adding posts