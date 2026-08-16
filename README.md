# Yellowstone Data Architecture

## Project Overview

This project focuses on designing and implementing a data architecture for Yellowstone National Park data.

The project combines relational database design, SQL implementation, data modeling, analytical querying, data documentation, and evaluation of a hybrid relational/non-relational architecture.

The existing relational design was implemented using PostgreSQL, while MongoDB was evaluated as a potential solution for selected semi-structured and evolving data.

## Project Objectives

The project focused on:

- Designing a structured relational database
- Modeling relationships between Yellowstone datasets
- Implementing the database using PostgreSQL
- Developing analytical SQL queries
- Documenting data elements through a data dictionary
- Creating logical and physical entity relationship diagrams
- Implementing database automation and access controls
- Evaluating PostgreSQL and MongoDB for different data requirements
- Developing a proposed hybrid database architecture

## Data

The project uses datasets related to Yellowstone National Park, including:

- Wildlife observations
- Observation periods
- Weather conditions
- Park locations
- Visitor information
- Monthly visitor counts

The repository includes the project datasets used for the database design and analysis.

## Database Design

The relational database organizes the data into related entities, including:

- Wildlife
- Observation Period
- Weather
- Visitors
- Monthly Visitors
- Wildlife Weather Impact
- Park Locations

Primary keys and foreign keys were used to establish relationships between entities and maintain data integrity.

## SQL Implementation

The PostgreSQL implementation includes:

- Database and schema creation
- Table creation
- Primary and foreign key relationships
- Constraints
- Data insertion
- Analytical queries
- Database trigger functionality
- Role-based access permissions

The SQL queries were used to explore patterns across wildlife observations, weather conditions, visitor activity, and seasonal data.

## Data Modeling

The project includes both logical and physical ERD designs.

The models document:

- Entities
- Attributes
- Primary keys
- Foreign keys
- Relationships
- Data types
- Database structure

A data dictionary is also included to document the fields, data types, nullability, and descriptions associated with the project's datasets.

## Relational vs. Non-Relational Architecture

The project evaluates whether selected Yellowstone data would benefit from a non-relational database approach.

PostgreSQL provides a strong structure for historical and highly relational data, while MongoDB was evaluated for data that may be semi-structured, rapidly changing, or require more flexible schemas.

The proposed architecture uses a **hybrid approach**, retaining structured historical data in PostgreSQL while considering MongoDB for appropriate semi-structured workloads.

## Database Automation

A PostgreSQL trigger was implemented to automatically recalculate annual visitor totals when monthly visitor records are inserted or updated.

This demonstrates the use of database-level automation to maintain derived information and reduce the need for manual updates.

## Access Control

The project also includes database roles with different levels of access.

Permissions were designed around different user responsibilities, including:

- Research users
- Park staff
- Database administrators

This demonstrates consideration of database security and controlled access to project data.

## Project Structure

```text
Yellowstone_Data_Architecture/
│
├── README.md
│
├── Data/
│   ├── Project datasets
│   └── Source data files
│
├── SQL/
│   └── Yellowstone_Database_Setup.sql
│
├── ERD/
│   └── Yellowstone_ERD_Diagrams.pdf
│
├── Data_Dictionary/
│   └── Yellowstone_Data_Dictionary.xlsx
│
└── Documentation/
    └── Yellowstone_Database_Architecture.pdf
```

## Programs & Tools Used

- **PostgreSQL** — relational database implementation
- **SQL** — database creation, querying, automation, and access control
- **MongoDB** — evaluated for non-relational data architecture
- **ERD modeling** — database structure and relationship design
- **Microsoft Excel** — data dictionary and data documentation
- **GitHub** — project organization and version control

## Skills Demonstrated

- Data architecture
- Database design
- Data modeling
- SQL
- PostgreSQL
- Relational database design
- NoSQL evaluation
- MongoDB architecture
- Entity relationship modeling
- Data dictionaries
- Analytical querying
- Database automation
- Database security and access control
- Data documentation
- Technical analysis
- Architecture evaluation

## Project Documentation

The complete architecture report is available in:

`Documentation/Yellowstone_Database_Architecture.pdf`

The repository also contains the project's datasets, SQL implementation, ERD documentation, and data dictionary.

## Project Context

This project was completed as part of a Data Architecture for Business Analytics course.

It demonstrates the process of moving from raw business data to a structured database architecture, implementing the design in SQL, analyzing the resulting data, and evaluating alternative database technologies based on different data requirements.
