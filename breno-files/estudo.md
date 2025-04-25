alias:: estudando, estudar, aprendendo, aprender

- [[banco de dados]]
	- TODO Assentar conceito de [[chave estrangeira]]
	- collapsed:: true
	  3. Tabela híbrida: colunas normalizadas com uma coluna JSONBinary
		- DONE #dúvida o que ele quis dizer com "create indexes on frequently queried columns"?
		  collapsed:: true
			- DONE #aprender sobre Indexing para grandes datasets
			  collapsed:: true
				- [[index]]
		-
	- collapsed:: true
	  2. tabela com estrutura normalizada
		- #questionamentos
		  collapsed:: true
			- TODO Qual a penalidade, exatamente, de se adicionar uma nova coluna?
			-
	- collapsed:: true
	  1. Tabela única com coluna JSONBinary
		- #questionamentos
		  collapsed:: true
			- TODO Qual a penalidade em termos de espaço de armazenamento?
			  collapsed:: true
				- Estamos usando o "TimescaleDB's columnar compression"?
				-
			- TODO E se o atributo adicionado também for precisar de performance com grandes datasets?
	- Como lidar com JSONB? https://dev.to/farakhshahid/working-with-json-in-postgresql-a-practical-guide-37aa
	- #inbox
	- collapsed:: true
	  > [[SQL]] Explained in 100 Seconds [(11) SQL Explained in 100 Seconds - YouTube](https://www.youtube.com/watch?v=zsjvFFKOm3c&t=26s)
		-
	- collapsed:: true
	  > 7 Database Paradigms [(11) 7 Database Paradigms - YouTube](https://www.youtube.com/watch?v=W2Z7fbCLSTw)
		- key value: redis memchached, etcd
		  logseq.order-list-type:: number
		- wide-collumn: cassandra, hbase
		  logseq.order-list-type:: number
		  collapsed:: true
			- logseq.order-list-type:: number
	- #roadmap
		- ### TODO Introduction to Databases
			- TODO What is a Database?
			- TODO Types of Databases (Relational, NoSQL)
			- TODO Basic Database Concepts (Tables, Rows, Columns)
			- TODO Database Management Systems (DBMS)
		- ### TODO SQL Fundamentals
			- TODO Introduction to SQL
			- TODO Basic SQL Commands (SELECT, INSERT, UPDATE, DELETE)
			- TODO Data Types in SQL
			- TODO Basic Querying (WHERE, ORDER BY, LIMIT)
			- TODO Joins (INNER JOIN, LEFT JOIN)
			- TODO Aggregate Functions (COUNT, SUM, AVG, MIN, MAX)
		- ### TODO Database Design
			- TODO Normalization (1NF, 2NF, 3NF)
			- TODO Entity-Relationship Model (ERD)
			- TODO Primary Keys and Foreign Keys
			- TODO Indexes
		- ### TODO PostgreSQL Essentials
			- TODO Introduction to PostgreSQL
			- TODO Installing PostgreSQL
			- TODO Basic PostgreSQL Commands
			- TODO Creating and Managing Databases
			- TODO Functions and Stored Procedures
			- TODO Transactions (BEGIN, COMMIT, ROLLBACK)
		- ### TODO Performance and Security
			- TODO Query Optimization
			- TODO Indexing Strategies
			- TODO User Management
			- TODO Roles and Permissions
			- TODO Backup and Recovery
		- ### TODO Advanced Topics and TimescaleDB
			- TODO Advanced Features of PostgreSQL (Full-Text Search, JSONB)
			- TODO Introduction to TimescaleDB
			- TODO Installing TimescaleDB
			- TODO Basic Concepts (Hypertables, Chunks)
			- TODO Time-Series Data Management
			- TODO Continuous Aggregates and Compression