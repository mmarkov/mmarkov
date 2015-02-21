---
layout: post
title: "Using cutomization prefixes for multiple add-ons in MS CRM 3"
excerpt: "Many add-ons in one MS CRM could co-exists with different prefixe"
tags: [.Net, MSCRM, Coding]
comments: true
---

## What is customization prefix
Actually I expect that my reader familiar with MS CRM, but anyway, I will give a short introduction first (I am just training my writing skills :).For real "meat" go ahead to Possible solutions section. Customization prefix is distinguishing attribute of any entity or attribute created by user, in schema all custom objects are prefixed with it. By default customization prefix is "New", so if you create entity with name Test actual entity name in database will be new_test. So far - so good.

## Why it is important?
Ok, it is important because usually all prefixes is hard coded in your code. Why? – because after adding customization webservice definition is changed and your custom entities appears as objects with cute methods and cute objects, so you use web reference in hardcode all prefuixes in your code.

## And what we should do in case of two Add-on’s?
Ok, but if we have to different addons and we need to deploy custom entities to CRM with two different prefixes. How can we do it. The most logical way go to Organization Settings, System Settings change prefix to \<addon1\> create entities then go to settings again and change prefix to \<addon2\>. But so far I saw more then 10 installations of MS CRM and none of them allowed such opperation :(.

Some people ask, Why not use import? But come on entities merge is not so reliable as creating entities manually. Will import merge OnCreate scripts on forms or new items in picklist for account type? Offcourse for empty CRM you can prepare customization pack, but do you belive that your addon is only customization used in CRM? Ritoric question, isn’t it.

##Possible solutions
Let’s try to solve it, since the most obvious way does not work.
Anyway merging does not work as expected, so we need to change prefix and do customization over existing system. Here is small piece of code that can help to modify prefix:
 
    // Set up the CRM Service.
    CrmService service = new CrmService();
    service.Credentials = new System.Net.NetworkCredential("my account","my password","my password");
    service.Url = "http://"+"my server"+":"+"my port"+"/mscrmservices/2006/crmservice.asmx";
    TargetRetrieveOrganization _organization = new TargetRetrieveOrganization();
    // prepare query to retrive organization
    RetrieveMultipleRequest req = new RetrieveMultipleRequest();
    QueryExpression query = new QueryExpression();
    query.ColumnSet = new AllColumns();
    query.EntityName = "organization";
    req.Query = query;     
    RetrieveMultipleResponse res =  (RetrieveMultipleResponse)service.Execute(req);
    organization curr_organization;
    String resultString = "Prefix was not updated";
    // there should be always only one organization, but lets teoretically work with collection
    foreach(BusinessEntity entity in res.BusinessEntityCollection.BusinessEntities)
    {
      curr_organization = entity as organization;    
      if ((curr_organization != null) && (curr_organization.name == organizationName.Text))
      {
         organization org = new organization();
         org.organizationid = curr_organization.organizationid;
         // set new prefix
         org.schemanameprefix = "my new prefix";
         service.Update((BusinessEntity)org);
         resultString = "Prefix was successfully updated";
      }
    }
    MessageBox.Show(resultString);

##Conclusion
Ok, now we have one more step to improve customization deployment, but it is a little step. WE are still faced with huge problems during deployment number of customization on the single CRM installtion. Can you imagine 10 – 15 addins to MS CRM installed together without conflicts. I have real concerns about "without conflicts" part.