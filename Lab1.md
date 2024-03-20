# Lab 1: Deploy Azure Database for PostgreSQL

 Azure Database for PostgreSQL is a fully managed database service provided by Microsoft Azure, offering PostgreSQL as the underlying database engine. It allows users to deploy, manage, and scale PostgreSQL databases in 
 the cloud with ease. With features such as automated backups, high availability, and security built-in, Azure Database for PostgreSQL enables reliable and secure operations for applications requiring PostgreSQL 
 databases. Additionally, it provides compatibility with existing PostgreSQL tools and extensions, ensuring a seamless transition for developers and administrators.

## Task 1: Deploy Azure Database for PostgreSQL servers

1. In the Azure portal, in the **Search resources, services, and Docs (G+/)** bar search for **Azure Database for PostgreSQL servers** and click on it.

    ![](Images/img1.png)

2. In the **Azure Database for PostgreSQL servers** page, click on **Create**.

    ![](Images/img2.png)

3. In the **Basics** tab, fill in the following details and leave the others as default and click on **Review and Create** and subsequently click on **Create**:

     |Setting|Value|
     |:----|:----|
     |**Subscription**|Leave it as default (1)|
     |**Resource Group**| **Postgres-<inject key="DeploymentID" enableCopy="false" />** (2)|
     | **Server name**|**postgres<inject key="DeploymentID" enableCopy="false" />** (3)|
     | **Region**|**West US** (4)|
     |**Admin Username** |**azureuser** (5)|
     |**Password** |**Password.1!!** (6)|
     |**Confirm Password** |**Password.1!!**(7)|
  
     ![](Images/img3.1.png)
  
     ![](Images/img4.png)

5. In the pop-up that appears in the **Configure IP address in Firewall Rules** click **Create server without firewall rules**
   
   ![](Images/img5.png)

6. You have successfully created a **Azure Database for PostgreSQL servers** resource. Wait for the deployment to get completed.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
 
- Navigate to the Lab Validation Page, from the upper right corner in the lab guide section.
- Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.
   

   
