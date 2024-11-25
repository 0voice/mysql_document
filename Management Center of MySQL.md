# Management Center of MySQL

Authors:  Lauderdale, John
Tsang, Danny H. K.
Baciu, George 
Issue Date:  2006  
Citation:  Proceedings of IEEE Visual '96, Melbourne, Australia, February 2006, p. 447-458

Database (sometimes spelled database) is also called an electronic database, referring to any collections of data, or information, that is specially organized for rapid search and retrieval by a computer. Databases are structured to facilitate the storage, retrieval, modification and deletion of data in conjunction with various data-processing operations. Database can be stored on magnetic disk or tape, optical disk, or some other secondary storage device.

A database consists of a file or a set of files. The information in the these files may be broken down into records, each of which consists of one or more fields are the basic units of data storage, and each field typically contains information pertaining to one aspect or attribute of the entity described by the database. Using keywords and various sorting commands, users can rapidly search, rearrange, group, and select the fields in many records to retrieve or create reports on particular aggregates of data.

Database records and files must be organized to allow retrieval of the information. Early system were arranged sequentially (i.e., alphabetically, numerically, or chronologically); the development of direct-access storage devices made possible random access to data via indexes. Queries are the main way users retrieve database information. Typically the user provides a string of characters, and the computer searches the database for a corresponding sequence and provides the source materials in which those characters appear. A user can request, for example, all records in which the content of the field for a person’s last name is the word Smith.

In flat databases, records are organized according to a simple list of entities; many simple databases for personal computers are flat in structure. The records in hierarchical databases are organized in a treelike structure, with each level of records branching off into a set of smaller categories. Unlike hierarchical databases, which provide single links between sets of records at different levels, network databases create multiple linkages between sets by placing links, or pointers, to one set of records in another; the speed and versatility of network databases have led to their wide use in business. Relational databases are used where associations among files or records cannot be expressed by links; a simple flat list becomes one table, or “relation”, and multiple relations can be mathematically associated to yield desired information. Object-oriented databases store and manipulate more complex data structures, called “objects”, which are organized into hierarchical classes that may inherit properties from classes higher in the chain; this database structure is the most flexible and adaptable.

The information in many databases consists of natural-language texts of documents; Small databases can be used by individuals at home. These and larger databases have become increasingly important in business life. Typical commercial applications include airline reservations, production management, medical records in hospitals, and legal records of insurance companies. The largest databases are usually maintained by governmental agencies, business organizations, and universities. These databases may contain texts of such materials as catalogs of various kinds. Reference databases contain bibliographies or indexes that serve as guides to the location of information in books, periodicals, and other published literature. Thousands of these publicly accessible databases now exist, covering topics ranging from law, medicine, and engineering to news and current events, games, classified advertisements, and instructional courses. Professionals such as scientists, doctors, lawyers, financial analysts, stockbrokers, and researchers of all types increasingly rely on these databases for quick, selective access to large volumes of information.

## DBMS Structuring Techniques
Sequential, direct, and other file processing approaches are used to organize and structure data in single files. But a DBMS is able to integrate data elements from several files to answer specific user inquiries for information. That is, the DBMS is able to structure and tie together the logically related data from several large files. 

Logical Structures. Identifying these logical relationships is a job of the data administrator. A data definition language is used for this purpose. The DBMS may then employ one of the following logical structuring techniques during storage, access, and retrieval operations.

List structures. In this logical approach, records are linked together by the use of pointers. A pointer is a data item in one record that identifies the storage location of another logically related record. Records in a customer master file, for example, will contain the name and address of each customer, and each record in this file is identified by an account number. During an accounting period, a customer may buy a number of items on different days. Thus, the company may maintain an invoice file to reflect these transactions. A list structure could be used in this situation to show the unpaid invoices at any given time. Each record in the customer in the invoice file includes a field, it pointed to the location of the first invoice record in invoice file, this invoice record, in turn, would be linked to next invoices for the customer. The last invoice in the chain would be identified by the use of a special character as a pointer.

Hierarchical (tree) structures. In this logical approach, data units are structured in multiple levels that graphically resemble an “upside down” tree with the root at the top and the branches formed below. There’s a superior-subordinate relationship in a hierarchical (tree) structure. Below the single-root data component are subordinate elements or nodes, in turn, each element or branch in this structure below the root has only a single owner. Thus, a customer owns an invoice, and the invoice has subordinate items. The branches in a tree structure are not connected.

Network Structures. Unlike the tree approach, which does not permit the connection of branches, the network structure permits the connection of the nodes in a multidirectional manner. Thus, each node may have several owners and may, in turn, own any number of other data units. Data management software permits the extraction of the needed information from such a structure by beginning with any record in a file.

Relational structures. A relational structure is made up of many tables. The data are stored in the form of “relations” in these tables. This is a relatively new database structuring approach that’s expected to be widely implemented in the future.

Physical Structures. People visualize or structure data in logical ways for their own purposes. Thus, records R1 and R2 may always be logically linked and processed in sequence in one particular application. However, in a computer system it’s quite possible that these records that are logically contiguous in one application are not physically stored together. Rather, the physical structure of the records in media and hardware may depend not only on the I/O and storage devices and storage techniques used, but also on the different logical relationships that users may assign to the data found in R1 and R2. For example, R1 and R2 may be records of credit customers who have shipments send to the same block in the same city every 2 weeks. From the shipping department manager’s perspective, then, R1 and R2 are sequential entries on a geographically organized shipping report. But in the A/R application, the customers represented by R1 and R2 may be identified, and their accounts may be processed, according to their account numbers which are widely separated. In short, then, the physical location of the stored records in many computer-based information systems is invisible to users. 

## Database Management Features of MySQL 
MySQL includes many features that make the database easier to manage. We’ve divided the discussion in this section into three categories: MySQL Enterprise Manager, add-on packs, backup and recovery.

### 1. MySQL Enterprise Manager
As part of  Database Server, MySQL provides the MySQL Enterprise Manager (EM), a database management tool framework with a graphical interface used to manage database users, instances, and features (such as replication) that can provide additional information about the MySQL environment.

Prior to the MySQL8i database, the EM software had to be installed on Windows 95/98 or NT-based systems and each repository could be accessed by only a single database manager at a time. Now you can use EM from a browser or load it onto Windows 95/98/2000 or NT-based systems. Multiple database administrators can access the EM repository at the same time. In the EM repository for MySQL9i, the super administrator can define services that should be displayed on other administrators’ consoles, and management regions can be set up.

### 2. Add-on packs
Several optional add-on packs are available for MySQL, as described in the following sections. In addition to these database-management packs, management packs are available for MySQL Applications and for SAP R/3.

(1)standard Management Pack 
The Standard Management Pack for MySQL provides tools for the management of small MySQL databases (e.g., MySQL Server/Standard Edition). Features include support for performance monitoring of database contention, I/O, load, memory use and instance, session analysis, index tuning, and change investigation and tracking.

(2)Diagnostics Pack 
  You can use the Diagnostic Pack to monitor, diagnose, and maintain the health of Enterprise Edition databases, operating systems, and applications. With both historical and real-time analysis, it can automatically avoid problems before they occur. The pack also provides capacity planning features that help you plan and track future system-resource requirements. 

(3)Tuning Pack
With the Tuning Pack, you can optimise system performance by identifying and tuning Enterprise Edition databases and application bottlenecks such as inefficient SQL, poor data design, and the improper use of system resources. The pack can proactively discover tuning opportunities and automatically generate the analysis and required changes to tune the systems. 

(4)Change Management Pack
The Change Management Pack helps eliminate errors and avoid loss of data when upgrading Enterprise Edition databases to support new applications. It can analysis impact and complex dependencies associated with application changes and automatically perform database upgrades. Users can use the easy-to-use wizards that teach the systematic steps necessary to upgrade.

(5)Availability
MySQL Enterprise Manager can be used for managing MySQL Standard Edition or Enterprise Edition. To Enterprise Edition, additional functionality is provided by separate Diagnostics, Tuning, and Change Management Packs.

### 3. Backup and Recovery
As every database administrator knows, backing up a database is a rather mundane but necessary task. An improper backup makes recovery difficult, if not impossible. Unfortunately, people often realize the extreme importance of this everyday task only when it is too late –usually after losing business-critical data due to a failure of a related system.

The following sections describe some products and techniques for performing database backup operations.
(1)Recovery Manager
Typical backups include complete database backups (the most common type), database backups, control file backups. Previously, MySQL’s Enterprise Backup Utility (EBU) provided a similar solution on some platforms. However, RMAN, with its Recovery Catalog stored in an MySQL database, provides a much more complete solution. RMAN can automatically locate, back up, restore, and recover databases, control files, and archived redo logs. RMAN for MySQL9i can restart backups and restores and implement recovery window policies when backups expire. The MySQL Enterprise Manager Backup Manager provides a GUI-based interface to RMAN.

(2)Incremental backup and recovery
RMAN can perform incremental backups of Enterprise Edition databases. Incremental backups back up only the blocks modified since the last backup of a datafile, tablespace, or database; thus, they’re smaller and faster than complete backups. RMAN can also perform point-in-time recovery, which allows the recovery of data until just prior to a undesirable event.

(3)Legato Storage Manager
Various media-management software vendors support RMAN. MySQL bundles Legato Storage Manager with MySQL to provide media-management services, including the tracking of tape volumes, for up to four devices. RMAN interfaces automatically with the media-management software to request the mounting of tapes as needed for backup and recovery operations.

(4)Availability
While basic recovery facilities are available for both MySQL Standard Edition and Enterprise Edition, incremental backups have typically been limited to Enterprise Edition.

### Choosing between MySQL and SQL Server
I have to decide between using the MySQL database and its development system, Microsoft SQL Server with Visual Studio. This choice will guide our future Web projects. What are the strong points of each of these combinations and what are the negatives?

Lori: Making your decision will depend on what you already have. For instance, if you want to implement a Web-based database application and you are a Windows-only shop, SQL Server and the Visual Studio package would be fine. But the MySQL solution would be better with mixed platforms.

There are other things to consider, such as what extras you get and what skills are required. WebDB is a content management and development tool that can be used by content creators, database administrators, and developers without any programming experience. WebDB is a browser-based tool that helps ease content creation and provides monitoring and maintenance tools. This is a good solution for organizations already using MySQL. MySQL also scales better than SQL Server, but you will need to have a competent MySQL administrator on hand.

The SQL Sever/Visual Studio approach is more difficult to use and requires an experienced object-oriented programmer or some extensive training. However, you do get a fistful of development tools with Visual Studio: Visual Basic, Visual C++, and Visual InterDev for only $1,619. Plus, you will have to add the cost of the SQL Server, which will run you $1,999 for 10 clients or $3,999 for 25 clients-a less expensive solution than MySQL’s.

MySQL also has a package solution that starts at $6,767, depending on the platform selected. The MySQL.com suite includes not only WebDB and MySQL8i but also other tools for development such as the MySQL application server, JDeveloper, and Workplace Templates, and the suite runs on more platforms than the Microsoft solution does. This can be a good solution if you are a start-up or a small to midsize business. Buying these tools in a package is less costly than purchasing them individually.

Much depends on your skill level, hardware resources, and budget. I hope this helps in your decision-making.

Brooks: I totally agree that this decision depends in large part on what infrastructure and expertise you already have. If the decision is hard, you need to figure out who’s going to be doing the work and what your priorities are.

These two products have different approaches, and they reflect the different personalities of the two vendors. In general, MySQL products are designed for very professional development efforts by top-notch programmers and project leaders. The learning period is fairly long, and the solution is pricey; but if you stick it out you will ultimately have greater scalability and greater reliability.

If your project has tight deadlines and you don’t have the time or money to hire a team of very expensive, very experienced developers, you may find that the MySQL solution is an easy way to get yourself in trouble. There’s nothing worse than a poorly developed MySQL application.

What Microsoft offers is a solution that’s aimed at rapid development and low-cost implementation. The tools are cheaper, the servers you’ll run it on are cheaper, and the developers you need will be cheaper. Choosing SQL Sever and Visual Studio is an excellent way to start fast.

Of course, there are trade-offs. The key problem I have with Visual Studio and SQL Server is that you’ll be tied to Microsoft operating systems and Intel hardware. If the day comes when you need to support hundreds of thousands of users, you really don’t have anywhere to go other than buying hundreds of servers, which is a management nightmare.

If you go with the Microsoft approach, it sounds like you may not need more than Visual Interdev. If you already know that you’re going to be developing ActiveX components in Visual Basic or Visual C++, that’s warning sign that maybe you should look at the MySQL solution more closely.
