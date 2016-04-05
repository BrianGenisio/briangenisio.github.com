---
title: 'Update for the ActiveRecord "Mock" Framework'
id: 47
categories:
  - Uncategorized
date: 2008-11-14 17:31:00
tags:
---

Back in July, I posted an article on how to [mock out the database in ActiveRecord](http://www.houseofbilz.com/archive/2008/07/22/active-record-mock-framework.aspx).&#160; The approach is simple, but is not mocking in the strict sense.&#160; I use SQLite in &quot;memory&quot; mode, creating a temporal database for testing.&#160; My colleague, [Jay Harris](http://www.cptloadtest.com/), has updated my code to allow for configuration in two ways.&#160; He has preserved my &quot;drop-in&quot; mode via the &quot;useDynamicConfiguration&quot; flag but when the flag is false, you can use a configuration from the app.config file. 

Here is the newest version of the code:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:404a40ca-5332-4e06-bfd1-5faf4b472e82" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">using</span><span style="color: #000000;"> System;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> System.Collections;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> System.Data;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> System.Reflection;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> Castle.ActiveRecord;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> Castle.ActiveRecord.Framework;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> Castle.ActiveRecord.Framework.Config;  
</span><span style="color: #0000FF;">using</span><span style="color: #000000;"> NHibernate.Connection;  

</span><span style="color: #0000FF;">namespace</span><span style="color: #000000;"> ActiveRecordTestHelper  
{  
  </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> ActiveRecordMockConnectionProvider : DriverConnectionProvider  
  {  
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> IDbConnection _connection;  

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> IConfigurationSource MockConfiguration  
    {  
      </span><span style="color: #0000FF;">get</span><span style="color: #000000;">  
      {  
        var properties </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Hashtable  
            {  
              {</span><span style="color: #800000;">"</span><span style="color: #800000;">hibernate.connection.driver_class</span><span style="color: #800000;">"</span><span style="color: #000000;">,  
                </span><span style="color: #800000;">"</span><span style="color: #800000;">NHibernate.Driver.SQLite20Driver</span><span style="color: #800000;">"</span><span style="color: #000000;">},  
              {</span><span style="color: #800000;">"</span><span style="color: #800000;">hibernate.dialect</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">NHibernate.Dialect.SQLiteDialect</span><span style="color: #800000;">"</span><span style="color: #000000;">},  
              {</span><span style="color: #800000;">"</span><span style="color: #800000;">hibernate.connection.provider</span><span style="color: #800000;">"</span><span style="color: #000000;">, ConnectionProviderLocator},  
              {</span><span style="color: #800000;">"</span><span style="color: #800000;">hibernate.connection.connection_string</span><span style="color: #800000;">"</span><span style="color: #000000;">,  
                </span><span style="color: #800000;">"</span><span style="color: #800000;">Data Source=:memory:;Version=3;New=True;</span><span style="color: #800000;">"</span><span style="color: #000000;">}  
            };  

        var source </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> InPlaceConfigurationSource();  
        source.Add(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;"> (ActiveRecordBase), properties);  

        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> source;  
      }  
    }  

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> ConnectionProviderLocator  
    {  
      </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> String.Format(</span><span style="color: #800000;">"</span><span style="color: #800000;">{0}, {1}</span><span style="color: #800000;">"</span><span style="color: #000000;">, TypeOfEnclosingClass.FullName,  
                                    EnclosingAssemblyName.Split(</span><span style="color: #800000;">'</span><span style="color: #800000;">,</span><span style="color: #800000;">'</span><span style="color: #000000;">)[</span><span style="color: #800080;">0</span><span style="color: #000000;">]); }  
    }  

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> Type TypeOfEnclosingClass  
    {  
      </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> MethodBase.GetCurrentMethod().DeclaringType; }  
    }  

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> EnclosingAssemblyName  
    {  
      </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> Assembly.GetAssembly(TypeOfEnclosingClass).FullName; }  
    }  

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">override</span><span style="color: #000000;"> IDbConnection GetConnection()  
    {  
      </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (_connection </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)  
        _connection </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">base</span><span style="color: #000000;">.GetConnection();  

      </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> _connection;  
    }  

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">override</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> CloseConnection(IDbConnection conn) {}  

    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;summary&gt;</span><span style="color: #008000;"> 
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> Destroys the connection that is kept open in order to keep the  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> in-memory database alive. Destroying the connection will destroy  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> all of the data stored in the mock database. Call this method when  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> the test is complete.  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;/summary&gt;</span><span style="color: #008000;"> </span><span style="color: #808080;">
</span><span style="color: #000000;">    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> ExplicitlyDestroyConnection()  
    {  
      </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (_connection </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)  
      {  
        _connection.Close();  
        _connection </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">;  
      }  
    }  

    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;summary&gt;</span><span style="color: #008000;"> 
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> Initializes ActiveRecord and the Database that ActiveRecord uses to  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> store the data. Call this method before the test executes.  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;/summary&gt;</span><span style="color: #008000;">
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;param name="useDynamicConfiguration"&gt;</span><span style="color: #008000;"> 
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> Use reflection to build configuration, rather than the Configuration  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> file.  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;/param&gt;</span><span style="color: #008000;"> 
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;param name="types"&gt;</span><span style="color: #008000;">  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> A list of ActiveRecord types that will be created in the database  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;/param&gt;</span><span style="color: #008000;">  </span><span style="color: #808080;">
</span><span style="color: #000000;">    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> InitializeActiveRecord(</span><span style="color: #0000FF;">bool</span><span style="color: #000000;"> useDynamicConfiguration,  
                                              </span><span style="color: #0000FF;">params</span><span style="color: #000000;"> Type[] types)  
    {  
      ActiveRecordStarter.ResetInitializationFlag();  
      IConfigurationSource configurationSource </span><span style="color: #000000;">=</span><span style="color: #000000;"> useDynamicConfiguration  
                                       </span><span style="color: #000000;">?</span><span style="color: #000000;"> MockConfiguration  
                                       : ActiveRecordSectionHandler.Instance;  
      ActiveRecordStarter.Initialize(configurationSource, types);  
      ActiveRecordStarter.CreateSchema();  
    }  

    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;summary&gt;</span><span style="color: #008000;"> 
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> Initializes ActiveRecord and the Database that ActiveRecord uses to  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> store the data based. Configuration is dynamically generated using  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> reflection. Call this method before the test executes.  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;/summary&gt;</span><span style="color: #008000;">  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;param name="types"&gt;</span><span style="color: #008000;"> 
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> A list of ActiveRecord types that will be created in the database  
    </span><span style="color: #808080;">///</span><span style="color: #008000;"> </span><span style="color: #808080;">&lt;/param&gt;</span><span style="color: #008000;">  </span><span style="color: #808080;">
</span><span style="color: #000000;">    [Obsolete(</span><span style="color: #800000;">"</span><span style="color: #800000;">Use InitializeActiveRecord(bool, params Type[])</span><span style="color: #800000;">"</span><span style="color: #000000;">)]  
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> InitializeActiveRecord(</span><span style="color: #0000FF;">params</span><span style="color: #000000;"> Type[] types)  
    {  
      InitializeActiveRecord(</span><span style="color: #0000FF;">true</span><span style="color: #000000;">, types);  
    }  
  }  
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

It can be downloaded from [Jay's site](http://www.cptloadtest.com/2008/10/30/UnitTestingActiveRecordApplicationsUsingMockDatabases.aspx): [ActiveRecordMockConfigurationProvider.zip](http://www.cptloadtest.com/ct.ashx?id=31b3c96d-5f73-4a20-bc3c-c155b5238071&amp;url=http%3a%2f%2fwww.cptloadtest.com%2fcontent%2fbinary%2fActiveRecordMockConnectionProvider.zip)