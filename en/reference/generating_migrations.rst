.. index::
   single: Generating Migrations

Generating Migrations
=====================

If you are using the Doctrine 2 ORM you can easily generate a migration class
by modifying your mapping information and running the diff task to compare it
to your current database schema.

If you are using the sandbox you can modify the provided `yaml/Entities.User.dcm.yml`
and add a new column:

.. code-block:: yaml

    Entities\User:
      # ...
      fields:
        # ...
        test:
          type: string
          length: 255
      # ...

Be sure that you add the property to the `Entities/User.php` file:

.. code-block:: php

    namespace Entities;

    /** @Entity @Table(name="users") */
    class User
    {
        /**
         * @var string $test
         */
        private $test;

        // ...
    }

Now if you run the diff task you will get a nicely generated migration with the
changes required to update your database!

.. code-block:: bash

    $ ./doctrine migrations:diff
    Generated new migration class to "/path/to/migrations/DoctrineMigrations/Version20100416130459.php" from schema differences.

The migration class that is generated contains the SQL statements required to 
update your database:

.. code-block:: php

    namespace DoctrineMigrations;

    use Doctrine\DBAL\Migrations\AbstractMigration,
        Doctrine\DBAL\Schema\Schema;

    class Version20100416130459 extends AbstractMigration
    {
        public function up(Schema $schema)
        {
            $this->addSql('ALTER TABLE users ADD test VARCHAR(255) NOT NULL');
        }

        public function down(Schema $schema)
        {
            $this->addSql('ALTER TABLE users DROP test');
        }
    }

The SQL generated here is the exact same SQL that would be executed if you were
using the `orm:schema-tool` task and the `--update` option. This just allows you to
capture that SQL and maybe tweak it or add to it and trigger the deployment
later across multiple database servers.

Ignoring custom Tables
======================

If you have custom tables which are not managed by doctrine you might face the situation
that with every diff task you are executing you get the remove statements for those tables
added to the migration class.

Therefore you can configure doctrine with a schema filter.

    $connection->getConfiguration()->setFilterSchemaAssetsExpression("~^(?!t_)~");
    
With this expression all tables prefixed with t_ will ignored by the schema tool.

If you use the DoctrineBundle with Symfony2 you can set the schema_filter option
in your configuration. You can find more information in the documentation of the
DoctrineMigationsBundle.



