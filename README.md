# OpenSourceDBBenchmark Script

This script is designed to run the [sysbench](https://github.com/akopytov/sysbench?tab=readme-ov-file#sysbench) against  RDS PostgreSQL/Aurora PostgreSQL  databases for pre-populated data. It provides various options to configure the database connection, sysbench parameters, and custom events for specific tables.

This would allow to simulate read and write workloads on the RDS PostgreSQL and Aurora PostgreSQL database instances allowing controlled benchmark testing.

## Usage

./refresh.sh -f <file.lua> -b <table1,table2,...> -u  <username> -d <database> -h <host> -p <port> -e <sysbench events> -t <sysbench threads> -w <password> -E <table1=events1,table2=events2,...> -O 


## Options

- `-f <file.lua>`: 
 Lua script file to be used by sysbench. If provided, sysbench will be run for this file.
- `-b <table1,table2,...>`: Specify a comma-separated list of tables for which sysbench should be run. The script will look for corresponding `.lua` files in the current directory. If a `.lua` script isnt available the execution would fail with error.
- `-u <username>`: 
 PostgreSQL username. Default is `postgres`.
- `-d <database>`: 
 PostgreSQL database name. Default is `postgres`.
- `-h <host>`: 
 PostgreSQL host endpoint. This option is required.
- `-p <port>`: 
 PostgreSQL port. Default is `5432`.
- `-e <sysbench events>`: 
 number of events for sysbench. Default is `1000`.
- `-t <sysbench threads>`: 
 number of threads for sysbench. Default is `100`.
 Refer [general-command-line-options in Sysbench](https://github.com/akopytov/sysbench/blob/master/README.md#general-command-line-options) for more details on events and threads options.
- `-w <password>`: 
 PostgreSQL password. This option is required.
- `-E <table1=events1,table2=events2,...>`:
 Specify a comma-separated list of table=events pairs to override the default events for specific tables.



## Default Values

- If the `-u` option is not provided, the default username is `postgres`.
- If the `-d` option is not provided, the default database is `postgres`.
- If the `-p` option is not provided, the default port is `5432`.
- If the `-e` option is not provided, the default number of events is `1000`, except for the `item_inventory.lua` file, where the default is `10000`.
- If the `-t` option is not provided, the default number of threads is `100`.

## Script Behavior

1. The script first checks if the required options (`-h` and `-w`) are provided. If not, it displays the usage information and exits.
2. If the `-f` option is provided, the script runs sysbench for the specified Lua file.
3. If the `-b` option is provided, the script runs sysbench for each table in the comma-separated list, looking for corresponding `.lua` files in the current directory.
4. If the `-E` option is provided, the script overrides the default events for the specified tables with the provided values.
5. After running sysbench, the script executes a series of `VACUUM` and `ANALYZE` commands on the `item_inventory`, `orders`, and `order_items` tables.

## Examples

Run sysbench for the `item_inventory.lua` file with default options:


```./script.sh -f item_inventory.lua -h <host> -w <password>```

Run sysbench for the `orders` with 5000 events  and `order_items` table with 10000 custom events:

```./script.sh -b orders,order_items -h <host> -w <password> -E orders=5000,order_items=10000```


Run sysbench for a specific file with custom username, database, and threads:

```./script.sh -f custom_file.lua -u myuser -d mydb -t 200 -h <host> -w <password>```

## Sample Data Description
- `item_category` - It is a lookup table which has two columns `category_id` (auto-generated integer serves as primary key) and category_name. We pre-populate this table with 10 rows.
`Primary key ( category_id )`
- `item_inventory` - It is the core table which is going to be referred in most of the pre-packaged workload simulation scripts. It is where we store inventory of the items along with details. The table stores `inventory_id (auto-generated bigint, also serves as primary key), item_name (255 varchar), item_count (integer), item_category (integer), dttm (timestamp), popularity_rank (bigint)`. The table has indexes:
`Primary key ( inventory_id )`
`idx_item_category ( item_category )`
`idx_inventory_rank ( item_category, popularity_rank DESC )`
- `orders` - It is the table where we will track user orders. Every order has a unique order_id (auto-generated bigint, also serves as primary key). One order may have multiple items. orders table will only store details such as userid, and delivery address and contact details (delivery_name , delivery_appartment_no, delivery_city, delivery_country, delivery_pincode, delivery_contact) and order details - order_dttm, order_status, and order_status_dttm. There is a pre-packaged script which can be use for placing orders - and will generated random data for delivery details, and update inventory table for items added to order. The table has indexes:
Primary key ( order_id )
idx_order_user_dttm ( userid, order_dttm )
- `order_items` - This is child of orders table (there is no foreign key implemented) and tracks items added for an order. There is no natural key and `order_items_idpk` an auto-generated bigint column is primary key. There can be multiple rows for each order in this table. The pre-packaged scripts use random function to generate number of items for each order - and it is skewed towards having one item per order.
For each item added to this table, ideally you would reduce the item_count in item_inventory table. But you can skip the update if you want to simulate an insert only pattern. `userid` and `order_dttm` are tracked for each item even though these are also available at the order level (this denormalization is for simplifying some batch workload). The table has indexes:
`Primary key (order_items_idpk)`
`idx_order_item_orderid ( order_id )`
`idx_order_item_user_dttm ( userid, order_dttm )`
- `users` - It is the table that stores user information who would place orders. The id is a bigint auto-generated value that serves as primary key, and is used in orders.userid, order_items.userid, and address.userid (there is not foreign key). It also stores username (pre-populated value uses email format data, but you can write custom scripts to populate other formats), `password_hash, first_name, last_name, date_of_birth, and default_address (referring to a row from address table that can be treated as default)`. The table has indexes:
`Primary key ( id )`
- `address` - It is the table that stores address information. In an ideal workload, an address will be selected from this table and copied into delivery address fields of orders table. But, for simplistic workload, you can generate random details of orders table. It has a primary key `address_id` which is auto-generated integer, `user_id` which refers to `users.id (no foreign key constraint)`, and `delivery address details (contact_name, street_address, appartment_no, city, pincode, country, and contact)`. The table has indexes:
`Primary key ( address_id )`
`idx_address_userid ( userid )`
`The default data generation scripts (which are also use in the CloudFormation template) will insert 100000 rows in users , and address tables. It will also insert 1 million rows in item_inventory.`
