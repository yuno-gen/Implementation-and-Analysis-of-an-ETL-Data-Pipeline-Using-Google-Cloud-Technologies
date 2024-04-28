# Implementation-and-Analysis-of-an-ETL-Data-Pipeline-Using-Google-Cloud-Technologies

1. Introduction
This report details the development of an ETL (Extract, Transform, Load) data pipeline using Google Cloud Platform services. The project demonstrates the integration of Cloud Data Fusion, Google BigQuery and lookerStudio to manage, analyse, and visualise big data effectively. In this project I have used the faker library of python to generate 500 employee records to perform the transformation and visualisation of using different tools of GCP which we will go through in the upcoming sections.


2. Background
The need for robust data management strategies in big data environments inspired the selection of this project. ETL pipelines are critical for data-driven decision-making and are integral to the efficient handling of large-scale data operations.
	The main initial problem that we deal with data extraction is converting it to a usable data format and when we are using apis and other data sources majority of the time it is in JSON format which is best when we are using noSQL but sometimes csv file is very easy to deal with when we are using dataframes styled analysis.



3. Methodology
	The following are the steps that I have used to create this project. You can also recreate this project just follow the given steps:

Data Extraction
Data Transformation (masking, join, encoding)
Data loading in Big Query
Data Visualization (lookerStudio)











Data Extraction:
We will create a Data Fusion instance, you just have to enter the name of the cluster and select the right region, and leave everything on default, it will take some time to run (~5mins).


	
	Once it starts running we move on to our next step.

	Now what we will write the script for extracting the data in a google collab notebook and converting it to csv format you can take the code from below or you can access it from this link: mgmt-alpande-final-project.ipynb
import csv
from faker import Faker
import random
import string
from google.cloud import storage


# Specify number of employees to generate
num_employees = 500
# Create Faker instance
fake = Faker()


# Define the character set for the password
password_characters = string.ascii_letters + string.digits + 'm'


# Generates employee data and save it to a CSV file
with open('employee_data.csv', mode='w', newline='') as file:
   fieldnames = ['first_name', 'last_name', 'job_title', 'department', 'email', 'address', 'phone_number', 'salary', 'password']
   writer = csv.DictWriter(file, fieldnames=fieldnames)


   writer.writeheader()
   for _ in range(num_employees):
       writer.writerow({
           "first_name": fake.first_name(),
           "last_name": fake.last_name(),
           "job_title": fake.job(),
           "department": fake.job(),  # Generates department-like data using the job() method
           "email": fake.email(),
           "address": fake.city(),
           "phone_number": fake.phone_number(),
           "salary": fake.random_number(digits=5),  # Generates a random 5-digit salary
           "password": ''.join(random.choice(password_characters) for _ in range(8))  # Generate an 8-character password with 'm'
       })


# Upload the CSV file to a GCS bucket
def upload_to_gcs(bucket_name, source_file_name, destination_blob_name):
   storage_client = storage.Client()
   bucket = storage_client.bucket(bucket_name)
   blob = bucket.blob(destination_blob_name)


   blob.upload_from_filename(source_file_name)


   print(f'File {source_file_name} uploaded to {destination_blob_name} in {bucket_name}.')


# Set the GCS bucket name and destination file name
bucket_name = 'mgmt-employee'
source_file_name = 'employee_data.csv'
destination_blob_name = 'employee_data.csv'


# Upload the CSV file to GCS
upload_to_gcs(bucket_name, source_file_name, destination_blob_name)


	Now search for Cloud Storage and create a bucket (Click create).



	Write the name of your bucket and hit create you don’t have to change anything. Now you have your bucket so just change the name of the bucket in the above mentioned code, I have also included the commands in the colab file for authentication so you won’t have to worry about it. After you run the code you can see that you have a file named employee_data.csv.


Now your data Fusion instance which is a dataproc instance only should be ready click on the view instance button it will ask you to authenticate yourself and you will land on the following page select Wrangle as we are now in the phase of transformation

In this project I have applied three data transformation:
Joined first_name and last_name in a new column as fullName
Masked the salary of the employees
Encoded the password of all the employees by using MD-5 encoding technique




This image shows the transformations that I have applied on this data:





You will also have to create a dataset on BigQuery as shown below:


Write the dataset name and hit create dataset button (this dataset name we will have to specify it in BigQuery properties in Wrangler.)
	

For the all the techniques that i have mentioned you just have to select the column you want to apply the transformation and click on the dropdown menu icon at the top of the column of any column(if you have selected more than one) and you will get the list of options from which you can choose from:

C. Data loading in Big Query

Then after you are done click on the create pipeline button at top right corner, it will give you two options select the Batch Pipeline and you will be direct to the next page as shown in the next image:
 
Here you have to select big query from the sink drop down section to be a place where all your processed data will be stored, tap it and just connect it with the Wrangler now click on properties on the BigQuery and here we will write all the necessary details as shown below:



Hit the validate button and then click on the Deploy button on the Cloud Data Fusion page. It will take you to the below page and it will take some time to run and you can see your employee_table in the dataset of BigQuery.


Here you can see the employee_table in the BigQuery dataset named employee.





d. Visualisation using lookerStudio
	Now we will visualise our dataset using lookerStudio, just go to lookerStudio and click on blank report and now you will see multiple options to use the data of your choice.  



Select bigQuery and you will see your dataset, select it and now select the table that we used till now as shown in the following screenshots: 






	Now you have your table loaded in the lookerStudio you can use the menu for multiple options for visualisations. I have used tables, pie charts and world charts for presentation of my data as shown below.


Results
The project effectively implemented an ETL data pipeline using Google Cloud Technologies, showcasing the seamless integration and functionality of Google Cloud services from data generation to visualisation.

Data Extraction
Faker Library Utilisation: By utilising the Faker library, we generated 500 realistic employee records. This dataset served as the foundation for our pipeline and allowed us to simulate the extraction of data from a dynamic source realistically.
Google Cloud Storage Integration: The generated CSV file was uploaded to Google Cloud Storage (GCS), ensuring that the data was accessible for further processing in Cloud Data Fusion. The seamless integration between the local Python environment (used for generation) and GCS showcased the flexibility and interoperability of Google Cloud services.

Data Transformation with Cloud Data Fusion
Data Masking and Encoding: Critical transformations included the joining of first and last names to create a full name, masking salaries to enhance privacy, and MD-5 encoding of passwords for security. These transformations were efficiently managed within Cloud Data Fusion, demonstrating its robust capabilities in handling data transformations securely and effectively.
Workflow Efficiency: The Data Fusion instance facilitated a smooth transformation process, converting the CSV data into a format suitable for analysis and storage in BigQuery. This step was critical in maintaining data integrity and preparing it for analytical queries and visualisation.

Data Loading into BigQuery
BigQuery as a Data Warehouse: After transformation, data was loaded into BigQuery, Google’s fully-managed data warehouse service. This step was crucial as BigQuery provides a powerful platform for running analytics at scale. The data loading process was streamlined through the batch pipeline feature in Cloud Data Fusion, illustrating the efficiency of GCP in managing data pipelines.
Dataset Integrity and Accessibility: The final dataset, housed in BigQuery, was intact, with all transformations applied correctly. This dataset was then ready for querying and extracting insights, proving BigQuery’s capability as an effective platform for storing and querying large datasets.

Visualisation with Looker Studio
Data Visualisation Capabilities: Using Looker Studio, various visualisations were created to represent the dataset effectively. This included tables, pie charts, and world charts, each providing unique insights into the dataset.
Interactive Reporting: The interactive capabilities of Looker Studio allowed for the dynamic exploration of data, making it a powerful tool for stakeholders to derive actionable insights from the visualised data. The ease of connecting Looker Studio with BigQuery and the intuitive interface facilitated a smooth visualisation process.



Discussion
		
		The ETL data pipeline project served as a powerful exploration of Google Cloud Platform's (GCP) capabilities in handling large datasets. This section dives into the project's outcomes, how it mirrored the course curriculum, and the roadblocks encountered during implementation.

ETL Pipeline Design: 
Lessons on data lifecycles and pipelines directly influenced the design and implementation of this ETL pipeline. The course teachings on GCP throughout the course and data pipeline management were particularly relevant in managing data flow and transformations within the project.


Extracting Meaning from Data
The seamless execution of data generation, transformation, loading, and visualisation using GCP tools showcased the platform's robustness and scalability for big data.  The smooth transition from raw data to actionable insights highlighted the effectiveness of a well-designed ETL process empowered by Google Cloud services. Looker Studio's dynamic visualisations unearthed key trends and patterns within the data, offering valuable decision-making support. This ability to transform raw data into an analytical-ready format underscores the potential of cloud technologies to fuel business intelligence and strategic initiatives.

Bridging Theory and Practice
Throughout the course, we delved into various aspects of big data management, including cloud computing, data manipulation, storage solutions, and visualisation techniques. This project offered a hands-on application of these concepts:

Cloud Data Fusion: Leveraging Cloud Data Fusion for data transformation tasks aligned perfectly with our studies on data integration tools and methods.  Its ability to handle diverse transformations like data masking and encoding mirrored the course emphasis on data security and privacy considerations.

BigQuery: BigQuery served as the project's data warehouse, echoing the course's exploration of scalable storage solutions and big data querying techniques.  Utilising BigQuery reinforced learnings on SQL and its adaptations for big data through Google's implementation.

Looker Studio: Looker Studio for data visualisation seamlessly tied into our discussions on data presentation and the importance of clear, user-friendly dashboards for non-technical audiences.

Navigating Roadblocks
Despite the project's successes, there were hurdles to overcome:

Integrating the Cloud: Initially, setting up and integrating the various GCP services presented a challenge due to the nuances of each platform's configuration. Mastering Cloud Data Fusion and Looker Studio involved a steeper learning curve, requiring additional time and resources.

Data Privacy Matters: Handling sensitive data, even simulated, necessitated significant effort.  Implementing proper data masking within Cloud Data Fusion to ensure privacy and adherence to data protection regulations was a critical task that demanded careful consideration and alignment with best practices.

Platform Limitations: Some limitations of GCP became evident, particularly in Cloud Data Fusion's scripting capabilities and Looker Studio's customization options.  These limitations necessitated workarounds or compromises within the project design.

Learning from Setbacks
The project wasn't without its setbacks, primarily related to underestimating the time complexity of service configuration and deployment.  Early attempts to integrate Cloud Data Fusion with BigQuery encountered configuration errors leading to data loading issues. These challenges proved valuable lessons, emphasising the importance of thoroughly reviewing service documentation and ensuring meticulous setup.

Similarly, initial data visualisation designs proved too complex, resulting in unclear presentations.  This misstep underscored the critical role of simplicity and clarity in data visualisation practices.





Conclusion

	The project aimed to design and implement an ETL data pipeline using the Google Cloud Platform (GCP) and successfully demonstrated the capabilities and efficiencies of integrating Cloud Data Fusion, Google BigQuery, and Looker Studio for managing, analyzing, and visualizing big data. This conclusion encapsulates the project’s achievements, challenges, and the synergistic application of theoretical knowledge and practical implementation.



References

VPC networks https://cloud.google.com/vpc/docs/vpc
Login on colab with gcloud without service account 
Use uniform bucket-level access https://cloud.google.com/storage/docs/using-uniform-bucket-level-access
GCP SDK installation https://cloud.google.com/sdk/docs/install-sdk
GCP SDK setup https://www.codingforentrepreneurs.com/blog/google-cloud-cli-and-sdk-setup/
Google Cloud Blog." Google Cloud Blog. Available online: https://cloud.google.com/blog
"Towards Data Science." Medium. Available online: https://towardsdatascience.com/
"Questions tagged 'google-cloud-platform'." Stack Overflow. Available online: https://stackoverflow.com/questions/tagged/google-cloud-platform
"Google Cloud Platform." YouTube. Available online: https://www.youtube.com/user/googlecloudplatform
