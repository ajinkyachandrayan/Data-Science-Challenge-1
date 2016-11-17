# Data-Science-Challenge-1

<< Automating IT Operations using Machine Learning >>

The Global Energy Management (GEM) BU combines all of the Group’s asset management and trading activities. It manages the Group’s assets and takes action in physical and financial energy markets while adopting a robust approach to risk management. In this way it enables the different BUs to secure the supplies needed for their activities and to manage their market risks. 

The Meteor suite is a GEM IT system used for the Front to Back chain.

MRM stands for Meteor Risk Management and is part of the Meteor suite. It is an application dedicated to producing PnL (Profits and Losses), VaR (Value at Risk) and other risk measures for Trading, Risk Market and Middle Office teams. This application uses a computation grid to allow faster and more efficient calculation of each job (a job being for example a Risk Analysis calculating PnL indicators for a certain portfolio or a VaR on a specific perimeter). To achieve this, a job will be split into various tasks which will then be sent to the calculation farm (each task on a different calculator : a calculator being an applicative instance that runs on a physical computer)

One physical computer will hosts hundreds of calculators (there are 1200 calculators available today on the farm). 

<< Schema : >>

1. Job created on client (the local MRM client is sending the job to the Request Admin server which will orchestrate the communication with the farm components)
2. DealSearch (to determine the deal perimeter the job will include based on the filters set by the user)
3. Split in tasks (determined by the technical parameters of the job)
4. Tasks submitted to calculators (the calculation part is then executed)
5. OLAP integration (calculation part is done, output is produced in an olap cube format)
6. Display (a client instance of excel is opened with a connection to the olap cube allowing to browse results and visualize them with the desired aggregation(s))

GEM IT's overall challenge is to better supervise IT operations in order to prevent workload spikes, allocate resources dynamically and identify recurring performance problems. To maintain good performance and high availability in a complex and constantly evolving environment, the GEM IT production teams face three main challenges:

1. monitor and manage the necessary application workloads
2. understand and simulate the behavior of applications to different workloads and system configurations.
3. detect, identify and resolve performance problems. 

The task of IT operators is to constantly monitor the applications, notice possible performance issues, and quickly resolve them because application downtime could potentially cost millions of dollars. The calculation grid is composed of 1200 nodes used for three types of computing :

1. Night scheduled treatments (25-35 jobs per night)
2. Treatments executed by users during the day (during the respective working hours in Singapore, Paris, Brussels and Roma)
3. Exceptional treatments launched once a month or to correct technical and functional malfunctions occurring during the night

The purpose of this challenge is to predict job execution duration (in minutes). This information will allow IT operations teams to anticipate deadlines and/or to review its execution plan.

<< Data Description >>

The scope of this use case is limited to a subset of jobs ran during 2014-2016. 

The jobs datasets (jobs_train.csv and jobs_test.csv) contains executions information. Each row correspond to a job execution. The key is the codtypjob. We have different type of jobs :

Codtypjob = 0 : it’s a valuation job.
Codtypjob = 1 : it’s a risk analysis job.
Codtypjob = 2 : it’s a VaR job.
Codtypjob = 3 : it’s an official valuation launched on full perimeter, i.e the scope of deals is all the deals from beginning of the current year.
Codtypjob = 4 : it’s an official risk analysis launched on full perimeter, i.e the scope of deals is all the deals from beginning of the current year.

The second dataset, resource_plan.csv, contains the number of calculators (slot) allocated to a given consumer during a certain period of time. A StartAllocation and EndAllocation correspond to the start/end hour of the allocation. 

We have 3 resource plans : 

* From January 2015 to March 2015
* From March 2015 to April 2016
* From April 2016 to today

Resource plan before January 2015 is not available.

For joining the datasets jobs and resourceplan, you can use the 3 last columns of the dataset jobs and the first column of the dataset resourceplanKeys for join :

* ArDailyStatConsumer (table jobs)
* VALDailyStatConsumer (table jobs)
* VARDailyStatConsumer (table jobs)
* Consumer (table resourceplan)

The third dataset, deals.csv, contains the number of deal traded each day by dealtype. As the official valuation (codtypjob = 3) and the official risk analysis (codtypjob = 4) are launched on all the deals from beginning of the current year,  the number of deals traded each day can be helpful for the model precision. Keep in mind that one of the dealtype which is the most time consuming for the farm is the EXFLEX.
