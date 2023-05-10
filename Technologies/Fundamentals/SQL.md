# SQL
## Relational Database
Each time a new table is added, there's always a left over of gaps from prior data not being added. 
| Name | Age | Location | Salary | DOB| 
| --------| -----|-----------| --------| -------|
| John | Doe | 45 | Louisiana | $500k |
| Gary | Innerarity | | | | | June |

# NoSQL 
Store information in the form of a document/page with Key:Value pairs with JSON formatting.
```json
{
	"name": "John Doe",
	"age": 45,
	"location": "Louisiana",
	"salary": 500000
}
```

| Key | Value|
---
# MySQL
port: 3306 (default)
## Installation
```bash
wget https://dev.mysql.com/get/...version.rpm

rpm -ivh mysql....version.noarch.rpm

yum install mysql-server

service mysqld start

service mysqld status
```

## MySQL Logs
```bash
# log location
cat /var/log/mysqld.log 
```