SAMPLE DOC (Partial Extract)
****************************

.. _acf:

Access Controls Framework
=======================================

.. contents::
    :depth: 2
    :local:

User Access Control involves multiple perspectives to evaluate if a business role has privileges to perform certain business activity or not. Even if the activity is allowed, there may be further restrictions based on level of authority, functional area or other restrictions.

The following criteria are key elements of Authorization Control Matrix:

- Activity - which task can / cannot be performed by a person in a process?
- Action - whether the person can just see the information or make changes?
- Organization Structure - which functional areas and entity are within working scope of the person?
- Process Parameters - whether user actions are limited to certain business scenarios?

Activity Controls
-------------------------------------------------------------------------------

User activities are mapped into application transactions. Activity Access Controls define matrix for authorization of activities that a user can perform. While mapping any business activity, this consideration should be kept in mind. One application transaction should not include multiple tasks that are performed by different users in any business scenario. This granularity allows segregation of duties and appropriate access controls when mapping user roles with application roles. This does not mean that activities that are always performed together are unnecessarily broken down in two separate application transaction.

Sometimes, multiple application transactions may be created to balance:

- restricting access to limited activity
- providing necessary access in one place.

This make sense where there is possibility of two people at different level having different level of access in a process step. For instance, consider the case of a purchase order with delivery schedule. A person in the role of purchaser may be allowed to create a purchase order with delivery schedule in a single step. It will be inefficient to create two steps, first, to create a purchase order, and second, to append a delivery schedule. But in certain organizations, delivery schedule is prepared and communicated separately to the vendor. In such cases, it is usually another person from purchasing, planning or logistics team who work out delivery schedule for the order. This person may not be authorized to alter any other term or condition of purchase order, except delivery schedule.

In these scenarios, two separate application transactions may be considered for a business activity - preparing purchase order in this case:

- First transaction for full purchase order maintenance with delivery schedule
- Second transaction for maintaining delivery schedule for an existing purchase order.

This is redundancy in application, but it creates necessary scope for controls and ease of work. The purchaser in above instance can create purchase order and append delivery schedule at the same step. The planner or logistics team member can act only on an existing purchase order with limited scope of maintaining the delivery schedule.

Each business process broken down into a series of activities should represent an executable transaction in the application:

- there is some input to the transaction
- there is some predefined processing involved in the transaction
- there is some output of the transaction

Each such step is represented by an transaction step in the system. This may be:

- Form: Data entry step where user data is entered on a screen form and saved to one or more data tables. This includes entry, editing, display and deletion activities.
- Print: Data set related to a business transaction or event is printed in a predefined document format.
- Report: Data set related to a series of business transactions or events is printed in predefined listing or summary format.
- Release: Data set or sets are presented in review format and data set status is updated.
- Interface: Data is processed between two different application systems via some predefined interface.

.. seealso:: See also :ref:`bpmr` for analysing and identifying business processes and activities for business process modeling and re-engineering.

Action Controls
-------------------------------------------------------------------------------

Any business activity involves few basic actions, commonly called CRUD actions:

- C: create a new record
- R: retrieve a saved record
- U: update an existing record
- D: delete an existing record

All business roles may not have privileges to perform all of above actions in an activity. In order to restrict users to perform only permitted actions, these actions are mapped in activities. When an application step (e.g., form or screen) is created for a business activity (e.g., creating a purchase order), user privileges are evaluated for possible actions and only permissible actions are exposed.

This may be achieved using either of two methods:

#. Adopt application step based on actions allowed for a user role. In this case, the same form or screen need to adopted for:

   - create mode for new data entry
   - retrieve mode for search and display existing record in read only format
   - update mode for search and display existing record in editable format
   - delete mode for search and delete existing record
   - additional modes such as print, release, activate, deactivate based on business scenarios.

#. Create separate steps in the application to perform the activity in limited mode:

   - create transaction for new data entry
   - retrieve transaction for search and display existing record in read only format
   - update transaction for search and display existing record in editable format
   - delete transaction for search and delete existing record
   - additional transactions such as print, release, activate, deactivate based on business scenarios.

Both approaches are equally effective; the difference is relevant from implementation and maintenance perspective. In first approach (single transaction with authorization matrix for actions) simplify application step from users perspective as there is only one application step. Application takes care of permissible mode based on authorization matrix. In this approach, the complexities shifts on technical implementation of implementation model. In second approach, the implementation is simple as one application step just need to focus only on one permissible action - CRU or D. In both cases, authorization matrix is same.

Normally a consistent approach should be followed for designing access control for `activities` and `actions` across the application. If an application is designed for use in different organizations and industries, only one of above approach may not be adequate for all implementations. In this case, albeit more complex but a hybrid approach may be adopted:

- All business `activity` is mapped to an application step (screen or form accessible from a menu option) with all possible CRUD actions. Access is evaluated at the entry point of the transaction privileges are modified before the data is presented for user `action`.
- Separate entry points (menu options) are also created for the application step in each CRUD action mode. Sometimes an entirely separate application step may be create for a specific mode such as retrieve and display a purchase order.
- User `activity` as well as CRUD `actions` are mapped in a user authorization matrix
- Power users are given access of the `activity` in all `action` modes, while users with limited privileges are given access of the `activity` with limited modes (such as only display of purchase orders).
- Alternatively, users with limited privileges may be given access for an application step for `activity` that is limited to just one `action` (such as only display of purchase orders).


Organization Unit Level Controls
-------------------------------------------------------------------------------

In a large organization, the operations are spread over multiple areas and separate persons may be responsible for these areas. The allocation of roles and responsibilities may be based on various criteria:

- Geographical Locations (different offices, cities, regions and countries)
- Separate of legal structure (different companies)
- Functional structure (business units, divisions, departments and cost centers)
- Logistics structure (warehouses, offices, factories)
- Team structures (production and maintenance teams, sales and purchase teams)

These structure and parameters are considered in the design of business application. These are incorporated as organization structure elements in relevant business transactions. Different organization structure elements and their mapping into application modules is covered in :ref:`osf`. There are different organization structure elements in different functional areas, such as `company` in financial accounting and `warehouse` in logistics operations.

Organization Unit level controls are implemented with help Organization structure elements. Each organization structure element is used as an access control object. The access control matrix (for organization structure elements) defines which business role has authorization to which all organization units. The application transaction starts with evaluating user roles and authorization matrix. Only if the user has access to an organization unit, next activity or action is allowed.

The combination of `activity` and `organization element` allows application access controls for specific activity in specific organization area.

.. seealso:: See also :ref:`osf` for common organization structure elements and their inter-dependence.

Process Parameters Controls
-------------------------------------------------------------------------------

The business processes have some variations in different scenario. The `terms of payment` in a purchase order is an example. In case of few vendors, purchase orders are issued on credit terms, while for some other vendors, advance payments may be allowed. The `payment term` is a process parameter which is specified by the user while performing the activity. In few business scenarios, all users may not access to handle all business scenarios. This should be handled by using configurable business process parameters and extending authorization checks on these parameter values.

While some parameters are generic which may be adopted for the purpose of this control, some other parameters must be specifically defined and standardized for the purpose of access controls. Document Number Series is one common example, which facilitates division of work and access controls. Different persons may be authorized separate document series for segregation of roles as well as access controls.

Process parameters should be considered in access controls only when it really adds value and controls to the business process and organization structure. As more and mode process parameters are included in access controls, the authorization matrix becomes complex and difficult to maintain and sustain.

Authorization Matrix, Transaction Groups and User Groups
-------------------------------------------------------------------------------

Once application transactions are finalized for each business activities, these should be grouped together in different business roles. Each transaction group may include one or more application transactions that can be assigned to a business role. Predefined transaction groups facilitates maintenance of user access controls. While defining transaction groups, the primary focus should be on business roles and the activities performed in that business role. The organizational positions and designations should be secondary consideration.

Similarly all users should be grouped into user groups, so that users with similar business roles can be considered together for provision of necessary application rights. These grouping may include groups of users with similar functions, or a specific position responsible for a restricted activity. For instance, basic purchasing activities may be included in a `purchaser` role, so that common purchasing tasks can be assigned together to buyers in the organization. Similarly, `purchase order approver` activity may be assigned in a separate transaction group, so that it can be assigned independently of purchasing actions. While defining user groups, the primary considerations should be organizational positions and designations.

When a transaction group is assigned to a user group, all activities under that transaction groups are available to the user group. Some activities may also be assigned directly to a business user. This is frequently applicable for approval and release related activities. The application access controls may include provision of direct assignment of a business transaction to a business user.

Industry Best Practices
-------------------------------------------------------------------------------

- Business Processes should be broken down to Activity level - one independent step that can be performed manually or automated with specified input, processing and output.
- Each business activity should be mapped to an application transaction. An application transaction may represent a form, report, print, release, interface, or conversion.
- Each activity should be evaluated for possible actions (create, retrieve and display, update and edit, deletion, print).
- Organization units should be mapped to organization structure elements.
- Process parameters should be evaluated with reference to process parameters. Process parameters should be mapped with help of internal or external configurable parameters.
- Authorization matrix should be prepared for possible combinations of business activities, actions, organization structure elements and process parameters
- There are two method of designing access controls matrix in the application:

  #. Application Transactions are assigned to Application Users. This is direct assignment of activities to users.
  #. Application Transactions are grouped together in Transaction Groups. Users are grouped together in User Groups. Transaction Groups are assigned to User Groups. This is indirect assignment of activities to users.



.. _osf:

Organization Structure Framework
================================

.. contents::
    :depth: 2
    :local:

All businesses are structured in a manner to facilitate adequate promotion of functional expertize and seamless flow of activities across those functions. As the organization grows, operations are organized into many groups. Each of these groups take care of certain part of overall business process. They receive triggers for actions from external partners (customers, vendors and other external agencies) and perform their role. They pass on the information and results to other internal or external agencies. Sub-division of operations may be required on account of many factors. Some common factors are:

- Expansion in different line of business (different products and services)
- Expansion in different geographic areas (different cities and countries)
- Formation of new companies for operational and regulatory reasons (different companies for different regions or ventures)
- Formation of specialized groups of people to perform certain functions (production teams, quality control teams, maintenance teams, accounting and such other functions)
- Formation of specialized groups to interact with a particular section of external partners and agencies (marketing teams, purchasing teams and so on)

One of the important considerations in this structuring is functional operations. The organization is structured into manageable departments that have their own objectives and at the same time they contribute to overall business objectives. The smaller groups in the overall organization structure can be grouped into following categories on the basis of functional operations:

- Logistics or Operations Organization

  Logistics or Operations departments perform the core operations of the business. Few common examples are sales, production, design, dispatch, maintenance, quality, supply chain departments

- Commercial Organization

  Commercial departments facilitates regulatory, external and commercial aspects of business processes. Few common examples are finance and accounts, purchase, legal departments

- Service Organization

  Service department provides specialized services to other departments. Few common examples are administration, human resources, information technology departments.

These departments play different roles in business functions. Therefore, one single organization entity cannot be used to represent entire organization structure in the application. We use multiple definitions of organization structure elements in the application. Each organization structure element is relevant for specific business scenarios. However it may be relevant in one or more than one business functions. Defining different elements for different purposes helps in many aspects:

- Access Controls (:ref:`acf`) can be mapped at desired level.
- Different business parameters (:ref:`ccf`) can be maintained for different entities.
- Same business object (:ref:`mdf` and :ref:`bdf`) can be managed in different organization units based on its own working environment.
- Regulatory compliance can be managed with respect to each organization unit independently.
- Business information can be analyzed and presented relevant to specific organization unit.
- Inter-dependency of organization units can be managed in a structured manner.


.. note::
   
   In the following section, each of the organization structure elements are described. We've included sample organization elements to provide an illustrative list of a hypothetical organization. This imaginary organization is considered to include common trade and manufacturing scenarios. 
   
   The same sample is considered in the development, training and implementation examples.

In the following sections, we'll describe organization structure elements in each functional area. We'll also specify alternative terminology, their relationship with other entities and common industry practices. We'll also provide few sample organization elements in each area. 

.... .... ....
   
   
.. _osf_oec:

Commercial Organization Elements
--------------------------------

Commercial departments provides commercial support to core operations and take care of ancillary requirements of the processes. Most of the times regulatory, external compliance, internal compliance and reporting functions are performed by these departments.

.. contents::
    :depth: 1
    :local:

.. _osf_cg:

Corporate Group
^^^^^^^^^^^^^^^

A corporate group is a general term to indicate multiple companies engaged in the business with a common majority ownership. 

Alternative Terminology
"""""""""""""""""""""""

- `Corporate Group` are also known as `business group`, `group of companies`, `business house`

Relationship with other entities
""""""""""""""""""""""""""""""""

- A Corporate Group is the top level entity in the organization. All other organization elements are subordinate to a Corporate Group.

Industry Practices
""""""""""""""""""

- Generally the brand name of the group is also used to commonly denote a corporate group.

Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Corporate Group
   :widths: 8 12 36
   :header-rows: 1

   * - Code
     - Entity Name
     - Additional Information
   * - DEMO
     - Demo Group 
     - An imaginary business group engaged in manufacturing, trading and service operations.


.. _osf_company:

Company
^^^^^^^

Company is an association of persons registered under local laws for trade and business activities and has its own independent legal entity. The liability of persons who have formed the company is limited to the money they have invested in their shareholding. 

The formation of company is driven by legal regulation framework and business needs. A company can undertake multiple business activities under the same name, so far as its charter allows. In these cases, more than one divisions or units are formed within the same company and those divisions or units are responsible for each line of business. It is also common to setup a separate company for a particular business operation. When the business decides to create multiple companies undertaking different business operations, the business is generally referred to as `business group` consisting of multiple `companies`. Generally one of the company is the `parent company` and the other companies are its subsidiary companies. These subsidiary companies are also called `sister concerns` of the main company under the business group.

Alternative Terminology
"""""""""""""""""""""""

- Companies are sometimes also called as firms. In strict terminology, a firm is an entity registered under local laws relating to partnership firms. Since the basic objective of both legal structures is same, these are sometimes used interchangeably.
- A company may operate in a single line of business or multiple lines of business. These `lines of business` (LOB) are also known as profit centers, divisions, business units (BU) or simply units.

Relationship with Other Entities
""""""""""""""""""""""""""""""""

- A :ref:`osf_cg` may consists of one or more companies.
- A company may own one or more :ref:`osf_au`. 
- A company may own one or more :ref:`osf_pc`.

Industry Practices
""""""""""""""""""

- Company formation is mostly influenced by the local compliance requirements for the business. Sometimes, separate companies are formed due to investment and taxation considerations.
- In most of the case of collaboration between two business groups (or between a government agency and a business group) a separate `special purpose company` is created for `joint venture` operations.

Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Companies
   :widths: 8 12 36
   :header-rows: 1

   * - Code
     - Entity Name
     - Additional Information
   * - DML
     - DEMO Manufacturing Limited 
     - An imaginary manufacturing company belonging to DEMO Group. DML is holding company of DTL, DSL and DOL.
   * - DTL
     - DEMO Trading Limited 
     - An imaginary trading company belonging to DEMO Group.
   * - DSL
     - DEMO Services Limited 
     - An imaginary service company belonging to DEMO Group.
   * - DOL
     - DEMO Overseas Limited 
     - An imaginary overseas company belonging to DEMO Group.


.. _osf_au:

Accounting Unit
^^^^^^^^^^^^^^^

The company maintains its books of accounts at the primary place of business. If a company operates from just one place and deals in only one line of business, a single set of books of accounts is sufficient. In following scenarios, there may be a need for different sets of books of accounts:

- The company has offices at different locations. These offices must record and report their financial transactions independently.
- The company has multiple lines of business, and each of these have separate managerial and operational teams. These units require separate set of books of accounts for internal monitoring and reporting at individual level.

The financial and accounting activities of the company may be organized into separate `accounting units` with its own set of books of accounts. Each accounting unit will have an independent books of accounts for recording and reporting its financial transactions and events. 

.. note:: 

   There is no need to setup a separate `accounting unit` for different `lines of business` (whether in same or different locations). It can be managed using the concept of :ref:`osf_pc`. Most current business application systems are able to handle recording and reporting at profit center level. 
   In few organizations, separate accounting units for different lines of business are still prevalent but only for legacy reasons.


Alternative Terminology
"""""""""""""""""""""""

- As `accounting units` closely corresponds to the business units structure, these are commonly know by the unit's name. 

Relationship with other entities
""""""""""""""""""""""""""""""""

- An `accounting unit` belongs to a :ref:`osf_company`.
- A :ref:`osf_company` may have one or more accounting units.
- An `accounting unit` may have one or more `plants`, `offices` or `warehouses` under its operational purview. 

Industry Practices
""""""""""""""""""

- The key factor is that the `accounting unit` has it's own set of books of accounts and can prepare its financial statements independently.
- It generally corresponds to a location for which independent `financial statements` need to be prepared.
- With more adoption of information technology and better business applications, the `accounts departments` operating at different locations are being consolidated. The number and location of accounting teams is still subordinate. The primary consideration is requirement of separate books of accounts. 

Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Accounting Units
   :widths: 8 12 16 20
   :header-rows: 1

   * - Code
     - Entity Name
     - Parent Company
     - Additional Information
   * - DML-CO-DEL
     - DML Corporate Office, Delhi 
     - DEMO Manufacturing Limited 
     - DML Corporate Office at New Delhi.
   * - DML-RO-DEL
     - DML Regional Office, Delhi
     - DEMO Manufacturing Limited 
     - DML Regional Office at Delhi.
   * - DML-RO-MUM
     - DML Regional Office, Mumbai 
     - DEMO Manufacturing Limited 
     - DML Regional Office at Mumbai.
   * - DML-RO-KOL
     - DML Regional Office, Kolkata
     - DEMO Manufacturing Limited 
     - DML Regional Office at Kolkata.
   * - DML-RO-CHN
     - DML Regional Office, Chennai
     - DEMO Manufacturing Limited 
     - DML Regional Office at Chennai.
   * - DTL-DEL
     - DTL Delhi Office
     - DEMO Trading Limited 
     - DTL Office in New Delhi.
   * - DSL-DEL
     - DSL Delhi Office
     - DEMO Services Limited 
     - DSL Office in New Delhi.
   * - DOL-US-DL
     - DOL Dallas Office, US
     - DEMO Overseas Limited
     - DEMO Overseas Dallas, US Office.

.. _osf_pc:

Profit Center
^^^^^^^^^^^^^

Profit Center is the business entity with reference to which revenue and costs are recorded, analyzed and reported. The Profit and Loss statement can be independently drawn with reference to the Profit Center. While the concept of profit center relates to revenue and costs, sometimes it is also extended to assets and liabilities. If all assets and liabilities are also identified with reference to the profit centers, it is also possible to draw up the balance sheet of each profit center.

Alternative Terminology
"""""""""""""""""""""""

- Profit Centers are also known as divisions, business segments, business units, lines of business.

Relationship with other entities
""""""""""""""""""""""""""""""""

- A company may have one or more profit centers.
- A profit center may be relevant for one or more than one companies.
- Profit Centers are divided into smaller entities to collect, analyse and report the revenue and cost data. Cost Centers is a common element used for this purpose.

Industry Practices
""""""""""""""""""

- Profit Centers are aligned to `business segments` or different `lines of business`
- All revenues and costs may not be directly allocatable to a specific profit center. A common profit center is sometimes used to collect common reporting items.

Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Profit Centers
   :widths: 8 12 36
   :header-rows: 1

   * - Code
     - Entity Name
     - Additional Information
   * - CPC
     - Cement Profit Center
     - Cement Profit Center
   * - PPC
     - Paper Profit Center 
     - Paper Profit Center 
   * - TPC
     - Textile Profit Center
     - Textile Profit Center
   * - EPC
     - Electrical Profit Center
     - Electrical Profit Center
   * - COMMON-PC
     - Common Profit Center
     - Common Profit Center

.. _osf_cc:

Cost Center
^^^^^^^^^^^

Cost Center is a division of Profit Center. It is the smallest level of the organization against which costs are recorded and reported. Although the concept of cost center is devised for collection and reporting of cost, it can also be used to record and report revenues.

Alternative Terminology
"""""""""""""""""""""""

- Cost Center are sometimes also known as departments. However, cost centers are more specific and lower division of an organization as compared to the concept of the departments.

Relationship with other entities
""""""""""""""""""""""""""""""""

- Cost Centers are grouped together into Cost Center Groups or Departments.
- Each Cost Center belongs to one and only one profit center.
- Each Cost Center may or may not be restricted to a company. Once the boundary of a cost center is frozen, it is generally not changed frequently.
- Many other organizational objects (such as products and materials, assets, equipments, locations) are assigned to cost center in order to facilitate recording of revenue and costs.

Industry Practices
""""""""""""""""""

- Cost Centers are grouped together into departments.
- Cost Centers are defined at the level at which cost recorded and reported.
- A huge number of cost centers is generally avoided as it leads to more recording and classification efforts. Similarly only few cost centers are also not effective for analysis purpose. 

Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Cost Centers
   :widths: 8 12 36
   :header-rows: 1

   * - Code
     - Entity Name
     - Department
   * - PRD-CMT
     - Cement Production
     - Production
   * - PRD-PPR
     - Paper Production
     - Production
   * - PRD-TXT
     - Textile Production
     - Production
   * - PRD-ELE
     - Electrical Production
     - Production
   * - MAINT-CIVIL
     - Civil Maintenance 
     - Maintenance
   * - MAINT-ELECT
     - Electrical Maintenance
     - Maintenance
   * - MAINT-MECH
     - Mechanical Maintenance 
     - Maintenance
   * - MAINT-INST
     - Instrumentation Maintenance 
     - Maintenance
   * - UTL-PG
     - Power Generation
     - Utilities
   * - FA
     - Finance & Accounts
     - Services

.. TBD: Extend the sample list


.. _osf_op:

Operations
^^^^^^^^^^

The commercial operations are classified on the basis of commercial model into following categories:

- Manufacturing Operations 

  Manufacturing of finished goods using raw materials and sometimes intermediate semi-finished goods. Use of manpower and equipments is a common characteristic. Inventories are owned by the business in entire supply chain.
  
- Trading Operations

  There is no manufacturing involved. The finished goods are purchased, stored and sold in the same form. The only change considered as part of trading operations is packaging. Inventories are owned by the business in entire supply chain.
  
- Services

  There are no products involved. The entity provides services to the customers.
  
- Job Work or Tolling

  The operations involves manufacturing on behalf of third parties. Input materials are provided by the customer and after conversion finished products are delivered back to the customer. The customer pays the conversion charges. Inventories are owned by the third parties in the supply chain.    
  
Alternative Terminology
"""""""""""""""""""""""


Relationship with other entities
""""""""""""""""""""""""""""""""

- Operations may cut across any other organization element:
  
  - any company may be engaged in any operation
  - any profit center may engage any operation model
  - any cost center may be relevant for any operation

- Unless there is a dedicated company, profit center or plant for an operations, any entity may be engaged in any operation in general. 

Industry Practices
""""""""""""""""""

- Sometimes, there are separate companies, units, or profit centers for these operations. In these cases, organization structure may be simplified in the business application if nature of operations is captured independently.
- In a new implementation, it should be considered as a separate entity.
- Sometimes, manufacturing and trading are subject to local regulations and registrations. In these cases, these operations are governed and restricted to registered operations only.


Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Operations
   :widths: 8 12 36
   :header-rows: 1

   * - Code
     - Entity Name
     - Additional Information
   * - M
     - Manufacturing
     - Manufacturing Operations
   * - T
     - Trading
     - Trading Operations
   * - S
     - Services
     - Service Operations
   * - JW
     - Job Work
     - Job Work & Tolling Operations


.. _osf_oel:

Common Logistics Organization Elements
--------------------------------------

Operations and Logistics departments are involved in the core business operations. These includes sales, production, delivery and customer service activities. 

.. contents::
    :depth: 1
    :local:

.. _osf_office:

Office
^^^^^^

Office is any location of the organization where any one or more of the following functions are carried out:

- Administrative functions, such as corporate office, head office, regional office, branch office.
- Operation and logistics functions such as manufacturing, procurement, storage, support.
- Sales and marketing functions such as show room, depot, warehousing and shipping. 

Office is a generic nomenclature for a physical location that belongs to the organization, whether rented or owned. 
An office may provide for one or more of above functions. There may be multiple offices in different locations for general and specific purpose.

Alternative Terminology
"""""""""""""""""""""""

Relationship with other entities
""""""""""""""""""""""""""""""""

- An office belongs to a company.
- It is also possible to host multiple companies in an office. It is a common practise but needs special consideration in application mapping.
- The office should be assigned to an accounting unit, so that all financial information can flow to company accounts.
- An office should belong to only one accounting unit. 
- An office can cover multiple factories, plants, warehouses, showrooms, workshops.

Industry Practices
""""""""""""""""""

- Every office should have an address and a contact person.
- The scope of activities performed in an office is generally pre-defined.
- When two or more companies operate from same office, each of these use office address with its own name. Similarly, a separate office entity should be defined in the application for each combination of company and office address.

Sample Organization Elements
""""""""""""""""""""""""""""

.. list-table:: Example Accounting Units
   :widths: 8 12 16 20
   :header-rows: 1

   * - Code
     - Entity Name
     - Parent Accounting Unit
     - Additional Information
   * - DML-CO-DEL
     - DML Corporate Office, Delhi 
     - DML Corporate Office, Delhi 
     - DML Corporate Office at New Delhi.
   * - DML-RO-DEL
     - DML Regional Office, Delhi
     - DML Regional Office, Delhi
     - DML Regional Office at Delhi.
   * - DML-RO-MUM
     - DML Regional Office, Mumbai 
     - DML Regional Office, Mumbai  
     - DML Regional Office at Mumbai.
   * - DML-RO-KOL
     - DML Regional Office, Kolkata
     - DML Regional Office, Kolkata
     - DML Regional Office at Kolkata.
   * - DML-RO-CHN
     - DML Regional Office, Chennai
     - DML Regional Office, Chennai
     - DML Regional Office at Chennai.
   * - DTL-DEL
     - DTL Delhi Office
     - DTL Delhi Office
     - DTL Office in New Delhi.
   * - DSL-DEL
     - DSL Delhi Office
     - DSL Delhi Office
     - DSL Office in New Delhi.
   * - DOL-US-DL
     - DOL Dallas Office, US
     - DOL Dallas Office, US
     - DEMO Overseas Dallas, US Office.

.. _osf_plant:

Plant
^^^^^

Plant is any location of the organization where any one or more of the following functions are carried out:

- Manufacturing including service and maintenance activities
- Inventory Storage and Supply Chain activities

A Plant is extension of an Office. While an office is a generic term, a plant specifically denotes a place where production, maintenance or inventory storage is carried out. An office location may consists of one or more plants.
The definition of plant is not limited to a manufacturing plant. It may also be involved in service rendering function (a software development center) or maintenance functions (such as automobile workshop).

Alternative Terminology
"""""""""""""""""""""""

- A plant is also known as factory, works, service center or delivery center in the context of manufacturing activities.
- A plant is also known as warehouse, showroom, godown, store or shop in the context of inventory storage and supply chain activities.
- Office and plants are used interchangeability. In a good business application framework, these are treated as separate entities to provide flexibility of grouping several plants into one office location. 
 
Relationship with other entities
""""""""""""""""""""""""""""""""

- A plant belongs to an office.
- An office may have several plants under its control.
- Since an office belongs to a specific company, the plant indirectly belongs to the same company.
- If two different companies have plants in the same vicinity, we'll need two separate plant definitions corresponding to respective offices and companies.

Industry Practices
""""""""""""""""""

- Every plant should have an address and a contact person.
- The scope of activities performed in a plant is generally pre-defined.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TODO: Plant Listing

.. _osf_sl:

Storage Location
^^^^^^^^^^^^^^^^

A Storage Location is the physical or logical sub-division of a plant to identify specific places where inventory is stored.

Alternative Terminology
"""""""""""""""""""""""

- Warehouse, Stores, Godown, Area, Storage Yard

Relationship with other entities
""""""""""""""""""""""""""""""""

- A storage location belongs to a plant.
- A plant may have several storage locations under its area.
- Since a plant belongs to a specific company, the inventory stored in that location indirectly belongs to the parent company.
- If two different companies have inventories in the same vicinity, we'll need two separate storage location definitions corresponding to respective companies.

Industry Practices
""""""""""""""""""

- Every storage location should have an address and a contact person.
- The scope of inventory stored in a storage location is generally pre-defined.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TODO: Plant Listing

Work Center
^^^^^^^^^^^

See :ref:`osf_wc` in :ref:`osf_oes`

.. _osf_dept:

Department
^^^^^^^^^^

A department is a logical grouping of people working in similar function with a specific skill set and usually under supervision of a a specific person. A department has a specific role in the business process and it may be interacting with internal or external agencies in performance of its role.
The department structure may be 

- free scope: performing same function for multiple companies, offices or plants
- restricted scope: some departments may have their role limited to specific companies, offices or plants

Alternative Terminology
"""""""""""""""""""""""

- Departments are also known as section, work groups or sometimes simply as groups. 
- In some business applications, departments are also defined as Cost Center Groups.

Relationship with other entities
""""""""""""""""""""""""""""""""

- A department may work for multiple companies, offices and plants. This may be subject of internal business rules and overriding controls.
- A department is generally divided into further into cost centers for micro level tracking and reporting of costs and other information.

Industry Practices
""""""""""""""""""

- The departmental structure is general not dependent on other elements. It is independent of companies, offices and plants. This is to allow operational layer of resources across those entities. 
- In some cases, the departments may have organizational boundaries and it varies from organization to organization. There restrictions depend on business needs and controls. In these cases, a department's role may be restricted to a specific function in a company, office or plant.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TODO: 

Cost Center
^^^^^^^^^^^

See :ref:`osf_cc` in :ref:`osf_oec`

Purchase Organization Elements
------------------------------

.. contents::
    :depth: 1
    :local:

.. _osf_po:

Purchase Organization
^^^^^^^^^^^^^^^^^^^^^

As the complexity and scale of purchase operations grows, it becomes necessary to separate the organization structure from Company. If purchase activities are spread across multiple geographies or input categories, it may be useful to have a separate top level entity for purchase activities.
A Purchase Organization is the entity which independently negotiates and finalize terms and conditions of purchase with the vendors. A purchase organization can define its own parameters and rules for dealing with any vendor segment or materials (and services) segment.
In a new setup, item categories and vendors should not be the basis of defining purchase organization structure, as there are other specialized approach for handling these considerations. Independent negotiation and contracting power should be the primary consideration for defining purchase organizations.

Depending on structuring of purchasing model of an organization, it can be used in different ways, for example:

- Usage on the basis of geographies: Asia, Europe, America
- Usage on the basis of internal structure: Global, Central, Local
- Usage on the basis of vendor segments: Chemicals, Spares, Contractors, Transporters

Generally there is a person responsible for a Purchase Organization. Therefore, a purchase organization may have its own contact and address information.

Alternative Terminology
"""""""""""""""""""""""

- Business Units, Purchase Departments, Sourcing and Contracts Departments are common equivalent of Sales Organization.

Relationship with other entities
""""""""""""""""""""""""""""""""

- A Purchase Organization may work for one or more specific companies. It may also be dedicated to a specific company.
- A Purchase Organization may source materials and services for one or more offices and plants.
- A Purchase Organization is the top level entity in sourcing and procurement functions. It has further subordinate entities to define controls and reporting.

Industry Practices
""""""""""""""""""

- Any independent purchase department working with full control over terms of sourcing is considered as a purchase organization.
- The scope of purchase organization may be defined by geographic area, internal consumers of materials and services (:ref:`osf_office` and :ref:`osf_plant`), or purchasing categories.
- A Purchase Organization can independently define the pricing and other terms and conditions of procurement.
- The location of purchase organization is generally not restricted by location of plants, offices and warehouses for which it procures the materials and services.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_pa:

Purchasing Area
^^^^^^^^^^^^^^^

A Purchasing Area is used to group together similar kind of purchasing activities. This is generally based on category of procurement.

Alternative Terminology
"""""""""""""""""""""""

- Purchasing Categories, Purchase Sections, Purchase Teams are common ways to organize purchasing areas.

Relationship with other entities
""""""""""""""""""""""""""""""""

- A purchasing area may cover an or more purchase organization, purchasing categories or purchasers.

Industry Practices
""""""""""""""""""

- Generally this is free floating elements which can be captured in all purchase transactions for reporting.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_pg:

Purchase Group
^^^^^^^^^^^^^^

Purchase Group represent a person who undertakes sourcing and procurement activities. Sometimes, a group of person responsible for specific categories or areas are also denoted as Purchase Group.

Alternative Terminology
"""""""""""""""""""""""

- Purchase Team, Sourcing Team, Vendor Management

Relationship with other entities
""""""""""""""""""""""""""""""""

- Purchase Groups may handle sourcing and procurement activities for all purchase areas. Sometimes, these may be limited to few specific purchase areas as per organizational needs.

Industry Practices
""""""""""""""""""

- Generally this is free floating elements which can be captured in any purchase transactions for reporting.
- Purchase Group are mostly defined at team level.

.. _osf_buyer:

Buyer
^^^^^

Buyer represents a team member who executes sourcing and procurement activities.

Alternative Terminology
"""""""""""""""""""""""

- Purchaser

Relationship with other entities
""""""""""""""""""""""""""""""""

- Buyer may handle sourcing and procurement activities for all purchase areas. Sometimes, these may be limited to few specific purchase areas as per organizational needs.

Industry Practices
""""""""""""""""""

- Generally this is free floating elements which can be captured in all purchase transactions for reporting.
- Buyers are mostly defined at individual level.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_oes:

Service Organization Elements
-----------------------------

Few services are so important to the operations that the business build their own infrastructure to provide these services. In manufacturing operations, it is common to setup internal utilities generation plants for captive use. Similarly maintenance operations are so important for uninterrupted and efficient running of the operations that internal maintenance teams are setup.

Service departments provides the facilities which can also be procured from external agencies. This internal arrangement is primarily done to provide experienced service at short notice. The cost is often an important consideration.

The separation of departments into Operations, Commercial and Service categories is very flexible. It purely depends on nature and size of operations and working environment of the business.

.. contents::
    :depth: 1
    :local:

.. _osf_wc:

Work Center
^^^^^^^^^^^

A Work Center is an assembly line, processing facility or similar production setup where some manufacturing process is performed. A Work Center may consists of one or more equipments. It receives a specified set of inputs to produce a specific output. Work Center may involve human and machine operations.

While the term Work Center is commonly used to denote a facility where some tangible products are produced, it may also include the facility where a specific service is rendered.

Alternative Terminology
"""""""""""""""""""""""

- Production Lines, Production Facility, Assembly Lines in manufacturing scenarios.
- Service Center, Work Shops in service scenarios.
- Equipments, Machines are also used in common reference.

.. note::
   `Equipment` and `Machines` are common nomenclature for a `Work Center` in common usage, however, in the context of a business application, the term `equipment` has a specific purpose which is subordinate to `Work Center`. A Work Center may consists of one or more equipments. This distinction is maintained to distinguish between `work center` as an organization structure element and `equipment` as the master data.

Relationship with other entities
""""""""""""""""""""""""""""""""

- A Work Center belong to a `Plant`.
- A plant may have one or more Work Centers.
- A Work Center usually consists of several equipments, machines or parts.

Industry Practices
""""""""""""""""""

- Work Centers are used to denote a facility where a specific product (including variants of the product) can be produced with a combination of inputs.
- Work Centers are grouped together according to the primary purpose, such as production, maintenance and services.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_mg:

Maintenance Group
^^^^^^^^^^^^^^^^^

A Maintenance Group is the team of people in the organization which undertake preventive, breakdown maintenance and service tasks. Generally there are several Maintenance Groups to support the Work Center setup in the areas of civil, electrical, instrumentation and mechanical maintenance activities.

Alternative Terminology
"""""""""""""""""""""""

- Support Services, Service Departments

Relationship with other entities
""""""""""""""""""""""""""""""""

- Maintenance Groups may provide services to one or more Work Centers.
- Generally maintenance groups belong to a `Plant`, but they may also provide service to more than one plant.

Industry Practices
""""""""""""""""""

- Maintenance Groups are generally organized on the basis of skill sets required for maintenance activities.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. note::
   `Plant` and `Storage Location` are also extensively used in service organizations. But these organizational elements are primary entities of logistics organization structure. See :ref:`osf_plant` and :ref:`osf_sl` in :ref:`osf_oel`.


Sales Organization Elements
---------------------------

Sales organization structure elements are used to organize, control and analyse sales and marketing activities.

.. contents::
    :depth: 1
    :local:

.. _osf_so:

Sales Organization
^^^^^^^^^^^^^^^^^^

As the complexity and scale of sales operations grows, it becomes necessary to separate the organization structure from Company. If sales activities are spread across multiple geographies or product lines, it may be useful to have a separate top level entity for sales activities.
A Sales Organization is the entity which independently negotiates and finalize terms and conditions of sales with the customers. A sales organization can define its own parameters and rules for dealing with any customer segment or product segment.
In a new setup, products and customers should not be the basis of defining sales organization structure, as there are other specialized approach for handling these considerations. Independent negotiation and contracting power should be the primary consideration for defining sales organizations.

Depending on structuring of sales and marketing model of an organization, it can be used in different ways, for example:

- Usage on the basis of geographies: Asia, Europe, America
- Usage on the basis of market segments: Products, Services
- Usage on the basis of customer segments: B2B, B2C

Generally there is a person responsible for a Sales Organization. Therefore, a sale organization may have its own contact and address information.

Alternative Terminology
"""""""""""""""""""""""

- Business Units, Sales and Marketing Departments are common equivalent of Sales Organization.

Relationship with other entities
""""""""""""""""""""""""""""""""

- A Sale Organization may work for one or more specific companies. It may also be dedicated to a specific company.
- A Sale Organization may execute delivery from one or more offices and plants.
- A Sale Organization is the top level entity in marketing and sale functions. It has further subordinate entities to define controls and reporting.

Industry Practices
""""""""""""""""""

- Any independent sales department working with full control over terms of sale is considered as a sale organization. 
- The scope of sales organization may be defined by geographic area, product segments, or customer segments.
- A Sales Organization can independently define the pricing and other terms and conditions of sales. 

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_dc:

Distribution Channel
^^^^^^^^^^^^^^^^^^^^

Distribution Channel is the route via which goods and services are delivered to the customers. Generally similar logistics, target groups, service conditions are the basis of defining distribution channel. Distribution Channel has significant impact of delivery cost and pricing matters.
Depending on modes of supply chain and areas of operations, it can be used in different ways, for example:

- Usage on the basis of commercial model of supply chain: Wholesalers, Distributors, Retailers, Direct Customers
- Usage on the basis of market segments with special considerations: Domestic, Exports, Special Zones 
- Usage on the basis of customer segments: Industrial Buyers, Consumers, Retail Chains

Alternative Terminology
"""""""""""""""""""""""

- Delivery Channels, Supply Chain are some less commonly used terms for Distribution Channel.

Relationship with other entities
""""""""""""""""""""""""""""""""

- Distribution Channels work in combination of sales organization. A sale organization may use one or more distribution channels to reach out to the customer.
- Distribution Channels are normally used across companies, offices and plants.
- While servicing a customer, a distribution channel may use one or more plants, warehouses or intermediaries.

Industry Practices
""""""""""""""""""

- Distribution Channels are strongly influenced by the supply chain model of the organization.
- Similar logistics and pricing considerations apply to a distribution channel.
- A distribution channel is not a physical entity, so mostly it does not have any specific address or location. 
- There may be a dedicated team for managing supplies via one or more distribution channels. 

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_sa:

Sales Area
^^^^^^^^^^

A Sale Area is the lowest level at which separate terms and conditions may be maintained in a sales scenario. A sales area represents the combination of:

- Sales Organization
- Distribution Channel
- Profit Center

Alternative Terminology
"""""""""""""""""""""""

Relationship with other entities
""""""""""""""""""""""""""""""""

- A Sales Area is defined at Sales Organization, Distribution Channel and Profit Center level.

Industry Practices
""""""""""""""""""

- Only active combination of Sales Organization, Distribution Channel and Profit Centers are defined.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_sof:

Sales Office
^^^^^^^^^^^^

Sales Offices are the interface for customer interaction. Generally a sales office has a business address and at least one contact person.

Alternative Terminology
"""""""""""""""""""""""

- Sales Offices include showrooms, branches, outlets and local stores.

Relationship with other entities
""""""""""""""""""""""""""""""""

- Sales offices may handle sales and marketing activities for all sale areas. Sometimes, these may be limited to few specific sales areas as per organizational needs.

Industry Practices
""""""""""""""""""

- If there is an inventory of products at a sales office, it is maintained as :ref:`osf_plant`.
- Generally this is free floating elements which can be captured in any sales transactions for reporting.
- This organization element is used for the purpose of analysis and reporting of sales data.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_sg:

Sales Group
^^^^^^^^^^^

Sales Group represent a person who undertakes sales and marketing activities. Sometimes, a group of person responsible for specific customers or areas are also denoted as Sales Group.

Alternative Terminology
"""""""""""""""""""""""

- Sales Team, Marketing Team, Customer Service

Relationship with other entities
""""""""""""""""""""""""""""""""

- Sales Groups may handle sales and marketing activities for all sale areas. Sometimes, these may be limited to few specific sales areas as per organizational needs.

Industry Practices
""""""""""""""""""

- Generally this is free floating elements which can be captured in any sales transactions for reporting.
- Sales Group are mostly defined at team level.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _osf_sp:

Sales Person
^^^^^^^^^^^^

Sales Person represents a team member who executes sales and marketing activities.

Alternative Terminology
"""""""""""""""""""""""

- Sales man or woman

Relationship with other entities
""""""""""""""""""""""""""""""""

- Sales Persons may handle sales and marketing activities for all sale areas. Sometimes, these may be limited to few specific sales areas as per organizational needs.

Industry Practices
""""""""""""""""""

- Generally this is free floating elements which can be captured in all sales transactions for reporting.
- Sales Person are mostly defined at individual level.

Sample Organization Elements
""""""""""""""""""""""""""""

.. TBD

.. _acf:

Access Controls Framework
=======================================

.. contents::
    :depth: 2
    :local:

User Access Control involved multiple perspectives to evaluate if a business role has privileges to perform certain business activity or not. Even if the activity is allowed, there may be further restrictions based on level of authority, functional area or other restrictions.

The following criteria are key elements of Authorization Control Matrix:

- Activity - which task can / cannot be performed by a person in a process?
- Action - whether the person can just see the information or make changes?
- Organization Structure - which functional areas and entity are within working scope of the person?
- Process Parameters - whether user actions are limited to certain business scenarios?

Activity Controls
-------------------------------------------------------------------------------

User activities are mapped into application transactions. Activity Access Controls define matrix for authorization of activities that a user can perform. While mapping any business activity, this consideration should be kept in mind. One application transaction should not include multiple tasks that are performed by different users in any business scenario. This granularity allows segregation of duties and appropriate access controls when mapping user roles with application roles. This does not mean that activities that are always performed together are unnecessarily broken down in two separate application transaction.

Sometimes, multiple application transactions may be created to balance:

- restricting access to limited activity
- providing necessary access in one place.

This make sense where there is possibility of two people at different level having different level of access in a process step. For instance, consider the case of a purchase order with delivery schedule. A person in the role of purchaser may be allowed to create a purchase order with delivery schedule in a single step. It will be inefficient to create two steps, first, to create a purchase order, and second, to append a delivery schedule. But in certain organizations, delivery schedule is prepared and communicated separately to the vendor. In such cases, it is usually another person from purchasing, planning or logistics team who work out delivery schedule for the order. This person may not be authorized to alter any other term or condition of purchase order, except delivery schedule.

In these scenarios, two separate application transactions may be considered for a business activity - preparing purchase order in this case:

- First transaction for full purchase order maintenance with delivery schedule
- Second transaction for maintaining delivery schedule for an existing purchase order.

This is redundancy in application, but it creates necessary scope for controls and ease of work. The purchaser in above instance can create purchase order and append delivery schedule at the same step. The planner or logistics team member can act only on an existing purchase order with limited scope of maintaining the delivery schedule.

Each business process broken down into a series of activities should represent an executable transaction in the application:

- there is some input to the transaction
- there is some predefined processing involved in the transaction
- there is some output of the transaction

Each such step is represented by an transaction step in the system. This may be:

- Form: Data entry step where user data is entered on a screen form and saved to one or more data tables. This includes entry, editing, display and deletion activities.
- Print: Data set related to a business transaction or event is printed in a predefined document format.
- Report: Data set related to a series of business transactions or events is printed in predefined listing or summary format.
- Release: Data set or sets are presented in review format and data set status is updated.
- Interface: Data is processed between two different application systems via some predefined interface.

.. seealso:: See also :ref:`bpmr` for analysing and identifying business processes and activities for business process modeling and re-engineering.


.. _ccf:

Configuration Controls Framework
================================

.. contents::
    :depth: 2
    :local:

Objectives of Configuration Controls Framework
----------------------------------------------

Configuration Control framework is an approach to change, extend or restrict business process parameters in the business application. Configuration controls represent the options which are predefined in the application, which can be used to influence how a business transaction is performed by a business user in certain scenario. These may be as simple as a predefined list (such as list of countries) or as complex as comprehensive planning models (such as material requirement planning) incorporated in the application.

Configuration parameters and options provide flexibility in the business applications. When an application incorporates a configuration option at design and development stage, it is possible to adopt certain changes in the business processes in future without changing the application. The extent of possible changes are restricted to the options included in the configuration design, it nevertheless provide a way to accommodate changing business needs without significant application changes at a later stage. This is more important when the application is deployed in live business scenarios and the ability to change a live scenario is subject of many change management issues.

The ability to configure different options in business functions is one of the key qualities of a mature business application. The more configurable options in the application, the more is the flexibility to accommodate future changes in same entity, in different businesses or even in different industries.

However, every configurable option needs significant inputs and efforts in terms of understanding, development, testing and training. As an application includes more configuration items, it becomes more complex. After a stage, it starts affecting usage and maintenance of the application. This brings another important consideration - configuration options are double-edged sword! They need careful consideration not only during development stage but also later at implementation stage.

This section covers the concept in details and another complete section follows with common and popular configuration elements. This **in no case** means that all of these options `must` be part of the business application; or, if these are available in the application, these `should` be activated in the implementation. `Rarely ever!` A careful and balanced approach is critical necessity for a feasible and practical solution.

This highly recommended balance can be maintained if we understand the objectives of the Configuration Control Framework. A balanced Configuration Control Framework facilitates the following purposes:

Standardization and Reporting
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Standard lists in configuration facilitate accurate information during the business transaction. When the list items are standardized, the searching, sorting, grouping and reporting functions are also improved.

Predefined Standards
""""""""""""""""""""
Predefined Standard lists and possible values provide consistency in data. An option with a static list of possible values is a good candidate for a configuration item. Configuration changes are rigid as compared to master data changes. If the changes are too frequent or the changes are maintained by the same users who use these options in business transactions, then it may be considered as a master data.
Country List a very common example. When the country name is entered by different users, they may choose abbreviation, partial names or full name based on their preference. In order to avoid inconsistencies in country data, it is a common practice to provide standard list of countries to choose an option from. When these lists are standardized, generally international standards are adopted as the basis. For instance, ISO codes are generally used for country codes and units of measurement.
Sometimes these standards lists are dependent on other list. Selection from first list, defines or restricts available options in the second list. Selection of region or states is dependent of selection of country in previous example. In these cases, dependent lists are prepared and used. In the user interface, the primary options are presented before dependent options.

Entry Aids
""""""""""

Entry aids facilitate faster data entry by reducing and avoiding data entry by the business user. If we analyze the information collected during a business transaction, we may find a set of information is based on a particular choice. This can be explained with the simple example of delivery address in an online shopping cart. The information about the address (apartment number, street address, locality, city, region and area codes etc.) is dependent on choice of address. A shopper with two delivery addresses, say home and office, need to enter complete address if there is no choice to define and use an address type. In order to avoid repetitive entry, it is a common practice to provide entry of an address with an address name or type. Once an address is defined, it may be selected by it's name and rest of the address information can be taken from the profile.
Similarly entry aids can be used as a short code for descriptive and complex options. This may help in reducing data entry efforts and errors. Allotment of roll numbers to students is a common usage of this pattern.

Personalization
"""""""""""""""

When users have their personal preference for a particular set of option, they may be provided `favourite` values of common option. In these cases, favourites, preferences or choices are allowed to be maintained in user profile. At the time of business transaction, these options are proposed from user preferences.
This personalization not only provide option to accommodate different usage patterns, but also helps as an entry aid.

Sort and Sequences
""""""""""""""""""

Once data values are assigned standard nomenclature, it is possible to sort, index, filter and report information with consistency. Once standard lists are defined, it is also possible to maintain multiple sequencing options for different purpose. This may be done in configuration data, master data or even transaction data. The relative implications are as follows:

- Options stored in configuration are generally static. Example, country or registration and local currency of the company.
- Options stored in master data are generally dynamic but with limited options. Example, allowed currencies with reference to customer orders.
- Options stored in transaction data are generally dynamic or not known until the time of transaction itself. The values may be facilitated by entry aids or personalization, but users may choose possible on case to case basis during each transaction. Example: mode of payment (cash or card) can be selected only after a customer interacts at check-out counter.

Process Controls
^^^^^^^^^^^^^^^^

Configuration items are effective way to maintain control over business processes. There are different level of controls that may be exercised during any step in a business process:

Prefixed Parameters
"""""""""""""""""""

Predefined parameters are forced during the process. The user has no choice but to accept the parameter and proceed with it. This is a common case with compliance items. Printing of Tax Registration Number of the company on commercial invoices is one such example.
In case of prefixed parameters, applicable values are defined in the configuration and presented as used as non-editable parameters during master data or transaction data steps.

Mandatory Parameters
""""""""""""""""""""

Mandatory parameters are required to be selected during the business process. The business users cannot bypass or ignore these parameters.

.. TBD Example

Recommendatory Parameters
"""""""""""""""""""""""""

Recommendatory parameters are optional and the users may ignore or bypass these parameters.

.. TBD Example

Dependent Parameters
""""""""""""""""""""

Dependent parameters may be mandatory or recommendatory depending on value of some other parameter.

.. TBD Example

Configurable Table Driven Design
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When a frequent information is maintained in a configuration table instead of coding into the application program, the application is more maintainable in future under different scenarios. With table driven design, it is possible to maintain different set of values in each implementation. There is no dependency on changes in the application code.

Granularity and Segregation of Roles
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Few users in business application have more authority over a business process on account of position, experience, or skill sets. At the same times, few other users are expected to follow predefined conditions. With provision of configurable parameters, it is possible to split information gathering and standardization based on user role. A user with authority may be provided access to maintain predefined values or possible values in the configuration, while other users may be restricted to use the predefined values provided in the configuration. Generally first category of users (sometimes called power users) are provided separate interface to maintain configuration values.

Process Variations
^^^^^^^^^^^^^^^^^^

Many parameters are dependent on organization entity where the business process is undertaken. In these cases, it is desired to maintain alternative or different values of the same parameter. In these scenarios, the data set is split based on organizational level and needs. Inventory levels is a common example of variations in planning parameters of a material in different :ref:`plants <osf_plant>`.
In these case, we split the data table of the object in multiple segments. While the common information is stored in the root segment, the information related to an organization entity is stored in a separate segment with reference to the respective entities.

Implementation Considerations
-----------------------------

.. _controls_ccf_flexi_conf:

Flexible Configuration Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A flexible configuration parameter is a setting which can be maintained with help of a user interface without any need to change in application program. This approach needs more initial efforts during design and development phase, but provide ease and sustainability in maintenance mode.
Let us consider the example of accounting aspects used in recording an accounting entry. Every accounting entry has two aspects: `debit` and `credit`. In business applications, we need to map these aspects for accounting entry operations. While programmers prefer to use `plus` and `minus` for these aspects, some organizations prefer to use `To` and `By` for `debit` and `credit` respectively. These aspects may be captured in a simple manner in configuration:

========================================================================================== =============
Option                                                                                      Value
========================================================================================== =============
Use `To` and `By` instead of `Debit` and `Credit` `(Tick for Yes, blank for No)`            [X]
========================================================================================== =============

If the indicator is active, the application will propose `To` for `Debit` and `By` for `Credit`. Internally `Debit` values will be stored as positive numbers and `Credit` values will be stored as negative numbers. All requirements are covered.
Now, what happens if this application is taken to an entity which does not use English as the main language? Everything is meaningless, unless programs are changed!
In a flexible configuration, the aspects will be moved to a recordset. The aspects, nomenclature and behaviour will be maintained in the recordset against an InternalID:

=============== =============== =============== ======= ==========  ============
Aspect Code     Aspect Name     Display Name    IsDebit IsCredit    InternalID
=============== =============== =============== ======= ==========  ============
DR              Debit           To              [x]     [ ]         1
CR              Credit          By              [ ]     [x]         2
=============== =============== =============== ======= ==========  ============

Now the aspects are selected from recordset, based active IsDebit or IsCredit indicator. Once the application design follow this convention, every aspect may be changed at any time in future. Since all references of aspects use `InternalID`, even `Aspect Code` can be changed without affecting any information.

Table Driven Options
""""""""""""""""""""

When configuration options are identified, these may be stored as part of main program, in a separate text files (or some other format such as xml) or in a table in the database. Since business applications invariably need database due to large volume of data they deal with, it is always a good practice to designate a table for a configuration and store possible options in the database.
This not only provide a common source and basis of single user interface, this also facilitates coverage of configuration data in backup procedures.

Extendability
"""""""""""""

When Configuration options are identified as recordsets and stored in a database table, it is possible to provide a separate user interface to extend configuration options and allow changes as per changing business needs.

.. _controls_ccf_rigid_conf:


Rigid Configuration Parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Few configuration parameters are used in the algorithms and therefore cannot be changed or altered without changes in the program itself. While flexible configuration parameters remove dependency on programs, sometimes this dependency may not be avoided. This may happen due factors such as involvement of complex algorithms or external references.

Material Requirement Planning has different models, and in each model separate parameters are used to project material requirements. When we define more than one planning model (such as need based planning, or reorder level planning), these a complete function to evaluate that planning model against a material. When we define a configuration option for `planning model`, the corresponding planning function needs to be defined in the application. In these cases configuration options are bound with a function. This is a rigid configuration parameter - this cannot be changed unless dependent function bindings are changed. Even if we define a new `planning model` in our configuration, it will not work unless it is bound with a planning function in the application.

Sometimes external or internal interfaces necessitates needs for rigid parameters. User authentication is a common example. When we use different authentication services (Local Database Users, Microsoft Active Directory, LDAP, OpenID and so on), every authentication mode has a specific application programming interface to validate user authentication. In this case, configuration options for `mode of authentication service` will be bound to each interface. It may be possible to keep certain dependent parameters of authentication service (such as ip addresses, ports, urls) as part of recordset, and thus improve configurability, but every new mode need to a extended in the application functions.

.. note::
   In The MyBAF, we will refer flexible configuration parameters as `external configuration` (exposed to external users with reference to programmers). Rigid configuration parameters will be called `internal configuration` (configuration that is internal to the program or function that utilize it).
   This distinction is necessary to understand which configuration will work without any change in application program and which configuration needs extension of application functions in order to enable new features.

Nomenclature
^^^^^^^^^^^^

Codes, Names and Descriptions
"""""""""""""""""""""""""""""

Codes can standardize any business object. Few codes are so popular that these are used across businesses (International Commercial Terms, or INCO terms such as CIF, FOB; currency codes such as USD, GBP; Units of measurement such as Kg, Ltr). When we define configuration options, the codes should be used as a piece of data that convey some information. In many business applications, these codes are also used as identifier of the information. A `piece of information` and an `identifier for that piece of information` are different. This implies that primary identification (primary id or primary key in terms of database table structures) should not contain any piece of information - it should mean just the identification. Identifiers are used in all linked references in other documents (and in turn, all over database tables related to those documents).

What happens when we use a code as identifier as well? If there is a change in a code (or coding pattern) due to some business needs, we'll need to adust all data stored in the database wherever those codes are stored as identifiers. When we separate every piece of information from the identifiers, it's usage is rectricted to internal database references. Now, it becomes possible to change the codes in the application at any point of time, without affecting referential integrity of database. Any code change efforts are also significantly reduced.

This approach may pose a challenge in few cases where original codes are required in some business documents (due to compliance or legal issues). In these cases, object codes may be copied into business document data at the time of creation of business documents. Both current codes and historically used codes are available for any reporting purpose with this approach.

Alpha vs Numeric Codes
""""""""""""""""""""""

Alpha-numeric codes are common as they convey some meaningful information. English is the common medium for alphabetic codes. This approach has its limitation when the same dataset is used by non-English users. If an implementation is designed to be used by users who are aware about English alphabets, alpha-numeric codes are recommended. If there is any chance that these code will be used by users who are not aware about English alphabets, only numeric codes should be used. There is a purpose why zip codes, ISD codes and phone numbers are all numeric! These pieces of information have an international audience and the communication is open for larger groups.

Alternate Language Name and Descriptions
""""""""""""""""""""""""""""""""""""""""

Many times it is necessary to provide alternative language names and descriptions for some business objects. Some of these usages are very common and well known - name and specifications of materials, name and addresses of customers and vendors. Many applications provide additional fields to capture namenclature in alternative language. But this approach becomes a restriction when the need extend beyond one language.

In order to keep local language translations independent of predefined options, it is always a better approach to identify language dependent fields in a dataset and mode these fields in another language dependent segment of that dataset. The additional data segment for language brings flexibility to extend translations of business objects to any language at any time.

.. note::
   This approach (language dependent data segments of a dataset) is used for business objects. The localization of user interface of business application is a different area and usually needs a different solution.

   Usually this aspect is handled by the development platform and framework and not dependent on database objects. Although some business applications use data tables for storage of translations of user interface.

.. TBD - Coverage of Application Localization

Maintenance Interface and Access Controls
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In smaller applications and implementation, it is a common practice to use a single table or file to maintain application configuration options. As the size of business and application complexity grows, this approach becomes a serious issue from maintenance and authorization perspective.

Some configuration options are not a simple combination of `option` and `value`. There may be different values for different organization entities. Also a single person may not be authorized to maintained all configuration parameters.

On account of these considerations, it is recommended to identify and create separate tables with necessary organization level segments. This tables should have their own maintenance interface linked to independent menu access points or business configuration transactions. With this approach, it is possible to assign maintenance of configuration options based on independent access controls matrix like any other business activity.

Usage Flow
^^^^^^^^^^

The configuration options can be used in organization structure elements, masters and transactions with varied level of controls and flexibility:

- Configuration in Organization Structure Elements

  A configuration option in organization structure is used to control process parameters without allowing business users any flexibility. For instance, the local currency of the company should be part of :ref:`osf_company` organization structure element. Once the local currency is defined in a company, the users are forced to follow the currency.

- Configuration in Master Data

  A configuration option in master data can be used with varying level of controls:

  - No proposed value and optional selection.
  - No proposed value but selection of one of the value is mandatory.
  - Proposed Value is provided by default, but it is optional. User may remove it and proceed.
  - Proposed Value is provided by default, but it can be changed. User may change it but not remove it.

- Configuration in Transaction Data

  Configuration parameters in transaction data may be exposed in following manners:

  - Default configuration is proposed from relevant organization structure element.
  - Default configuration is proposed from master data elements.
  - The Configuration is directly selected in the transaction data.

  In above cases, the configuration parameter may be mandatory or optional depending on configuration scenario. In few cases, the configuration option may itself be configurable if the business scenarios may need it in some cases while may ignore in other. In such cases, it is advisable to provide the option at relevant organization structure element whether the specific configuration option is mandatory or not.

Dependent Parameters
^^^^^^^^^^^^^^^^^^^^

When a specific configuration option is selected, it may require additional information to complete the business function. The additional information is considered as dependent parameters. These dependent parameters may be one of following:

- Another a configuration option

  Dependent parameters is also a value controlled by another configuration option. In this case, dependent configuration is exposed as mandatory or optional items.

  *Example*: When `Reorder Level Planning` is selected in Material Master for `Material Planning Model`, we also need `Order Lot Size`. The `Order Lot Size` has multiple options. `Order Lot Size` configuration is dependent of `Material Planning Model` configuration.

- Another Business Process Parameter

  Dependent parameter may be a norm for the main configuration selected. These parameters are normally exposed in master data if they represent a norm.

  *Example*: When `Reorder Level Planning` is selected, we also need `Reorder Level Quantity` for calculation of material requirement. `Reorder Level Quantity` is a parameter in Material Master which is dependent on a particular value of `Material Planning Model`.

- Additional Transactional Information

  Additional information required to complete the business transaction is exposed in the business transaction step. It may be optional or mandatory depending on design and configuration.

  *Example*: There are different duty structures in case of local procurement and imports from other countries. When imported mode of procurement is selected, the we need applicable duties and rates to calculate total cost of procurement.

Changes in Configuration Values
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once a configuration option is designed and used in a business application, it may be used in business reports. Business reports essentially provide historical data for information and analysis. Since business scenarios are dynamic, many parameters changes over time. This brings an important consideration on storage strategy of a configuration parameter. There are two approach of storing and using a configuration parameter:

- Store configuration parameter value at the level of organization structure element or master data segments. In business transaction reports, read the chosen value from organization structure or master data, as the case may be.
- Copy the configuration parameter value from organization structure element or master data segment and store chosen value in the business transaction. The business reports use the value from the business transactions.

Retrospective Changes
"""""""""""""""""""""

If we need changes in reported business parameter value with retrospective effect, we should store and read the values from source - organization structure element or master data segments. When the values are changed at source level, it will be reflected in past and future transactions.

Prospective Changes
"""""""""""""""""""

If we need changes in reported business parameter value with prospective effect only, we should copy, store and read the values from transaction data. When the values are changed at source level, it will not affect the values stored in the past transactions. Business reports may still provide information on parameter options that existed at the time of transaction.

A Word of Caution
-----------------

Configuration Parameters are a powerful tool to enhance application scalability and capabilities. In practical scenarios, every configuration parameter adds complexity to business logic. It needs careful design considerations and significant development, testing and maintenance efforts. The design, development, testing and maintenance efforts should justify the addition of a configuration parameter. Just because a configuration parameter provide more flexibility and power does not necessitates its implementation. Similarly, we can also assess the complexities and impact of adding a configuration parameter at later stage of application development and deployment stages.

If the usage of a configuration parameter is not expected in foreseeable future, and it may be added in future with reasonable efforts, it is better to save the efforts for future when the need actually arrives. If the configuration parameter is commonly used in business scenarios in target deployments, it may be useful to include it in design.

Maintenance Roles and Responsibilities
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The roles and responsibilities of maintenance teams may provide important insight into classification of objects into Configuration Objects and Master Data Objects. Generally sensitive and critical process parameters considered part of Configuration Objects and the maintenance roles and responsibilities are restricted to power users who understand the implication of configuration. The configuration data changes are generally subject to change control procedures. The parameters that are not sensitive and business critical are generally classified as master data and the maintenance is not subject to repeated test and validation when new data is created.

Since business applications have been in usage for a long period and reached a reasonable level of maturity, this Framework makes necessary assumptions and classify configuration and master data based on prevailing and best business practices.

Maintenance Nightmare
^^^^^^^^^^^^^^^^^^^^^

Some configuration parameters are so dynamic that frequent changes are required in the possible values. It may not be feasible to apply change control procedures to these configuration parameters. The master data objects are generally subject to less rigorous change control procedures as compared to configuration objects.

It may be more prudent to evaluate and consider of a configuration parameter can be better maintained as master data instead of a configuration data. A common example is geographic locations - while it is a common practice to include country list in the configuration objects, it may not be a feasible practice to maintain city list as a configuration object. The list of cities may be classified as master data as subject to easier maintenance.

Short and Long Names
^^^^^^^^^^^^^^^^^^^^

Many configuration options are required in business reports. The codes and descriptions of these configuration parameters are also include in those business reports. While long descriptions is useful at the time of business transaction for correct understanding and selection of a parameter, long descriptions may clutter the reports and reduce their readability and effectiveness.

In order to address this aspect, both short names, long names and descriptive text may be provided in configuration records:

- Short Names can be used in business reports to keep these compact.
- Long Names can be used in business transactions to present sufficient information for correct selection.
- Descriptive Text may be used for providing additional help and instruction for the users who may be interested in more details about the choice and the implications.

Flexibility vs Rigidity
^^^^^^^^^^^^^^^^^^^^^^^

The configuration parameters bring controls and discipline in application usage. The business users are restricted to choose arbitrary values when they execute a business transaction in any business scenario. Sometimes this control and rigidity become a constraints on usability of the application.

In these cases, the configuration element should be carefully analyzed to segregate control, norms and transactional information parts. These can be split as necesary and mapped to configuration data, master data and transaction data level.

Example: International Commercial Terms (INCO Terms) is a common clause in the international trade. There are predefined codes for commercial and delivery aspects. The INCO term code consists of two parts:

#. INCO Term Clause - CIF, FOB, C&F etc.
#. Destination - the name of the location with reference to which the clause is applicable.

Since destination will keep changing for different customers and vendors, INCO Terms may be split into two parts:

- INCO Term Clause with predefined values as part of Configuration Data.
- INCO Term Destination as additional information maintained in the customer and vendor masters.
- INCO Term Clause and Destination both captured at the time of a customer or vendor business transaction. INCO Term clause can be selected by the business user from the preconfigured list of allowed terms and destination provided by the business user depending on a particular business transaction.

Common Configuration Elements in Details
----------------------------------------

In the above section, we've covered the objectives, implementation considerations and precautions of business configuration framework. The list of configuration elements that have been used in business applications is quite comprehensive. Further, these is significant variations in the business practices and the implementation approaches in existing business applications.

We have considered a separate section to compile a comprehensive repository of configuration elements in a mature business application. In the next section :ref:`configuration`, we also cover,

- common configuration elements in each functional area
- objective of the configuration element and possible options,
- maintenance level at organization element, master data and transaction data,
- implications and impact of configuration,
- maintenance roles and responsibilities,
- integration aspects in different business modules and processes.
