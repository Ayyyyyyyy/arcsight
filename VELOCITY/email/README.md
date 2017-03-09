
# ArcSight_Velocity
### Additional resources
+ http://velocity.apache.org/engine/1.7/user-guide.html#variables
+ http://docs.oracle.com/javase/7/docs/api/java/lang/String.html

#### Velocity templates for email notifications in ESM

Step 1: Create new directory in your ESM's notification directory (eg: `/opt/arcsight/manager/config/notification/custom`)

Step 2: Navigate into the new directory, and create a new vm (or re-use newtemplate.vm) for a custom rule.

Step 3: Customize the Email.vm template to reference the new template file you created in step 2.

Step 4: All set - The manager DOES NOT require a restart when you make changes to these templates.
