# Cheat sheet .NET application with Database on WISA stack #
----

## Prerequisites ##
For this cheat sheet, we used [this](http://www.asp.net/mvc/overview/older-versions/deployment-to-a-hosting-provider/deployment-to-a-hosting-provider-introduction-1-of-12 ".net guide") guide and project.

If you want to follow this guide, please download the provided project and make sure you meet the prerequisites stated there.

## Download/write your application ##
Download or write the project you want to deploy on your stack.

Open your project.

## Configure appSettings in web.config ##
We're using Code First Migrations so we can remove the second add line

     <appSettings>
      <add key="Environment" value="Dev" />
      <add key="DatabaseInitializerForType ContosoUniversity.DAL.SchoolContext, ContosoUniversity.DAL" value="ContosoUniversity.DAL.SchoolInitializer, ContosoUniversity.DAL" />
    </appSettings>
    
## Add Code First Migrations ##
From the Tools menu, click Library Package Manager and then Package Manager Console.

At the top of the Package Manager Console window select ContosoUniversity.DAL as the default project and then at the PM> prompt enter "enable-migrations".

