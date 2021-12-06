WMI

    int main(int /*argc*/, char */*args*/[])
    {
        try {
            Win32_ComputerSystem computer = retrieveWmi<Win32_ComputerSystem>();
            Win32_ComputerSystemProduct product  = retrieveWmi<Win32_ComputerSystemProduct>();
            SoftwareLicensingService liscense  = retrieveWmi<SoftwareLicensingService>();
            Win32_OperatingSystem os_info  = retrieveWmi<Win32_OperatingSystem>();

            cout<<"Computername: "<<computer.Name<<" Domaind:"<<computer.Domain<<endl;
            cout<<"Product: "<<product.Name<<" UUID:"<<product.UUID<<endl;
            cout<<"Architecture: "<<os_info.OSArchitecture<<std::endl;
            cout<<"Roles: "<<endl;
            for(const string role : computer.Roles)
            {
                cout<<" - "<<role<<std::endl;
            }
            cout<<endl;
            cout<<"Installed services:"<<endl;
            for(const Win32_Service &service : retrieveAllWmi<Win32_Service>())
            {
                cout<<service.Caption<<endl;
            }
        } catch (const WmiException &ex) {
            cerr<<"Wmi error: "<<ex.errorMessage<<", Code: "<<ex.hexErrorCode()<<endl;
            return 1;
        }
    
        return 0;
    }
```
The include file <wmi.hpp> contains all interfaces to execute WMI queries.
The include file <wmiclasses.hpp> contains some predefined WMI classes
(e.g. Win32_ComputerSystem or Win32_Service...)

Create WMI classes
------------------
As already mentioned, the include file <wmiclasses.hpp> provides some standard
WMI classes, but it is also very easy to add more of them. All you need to do is:
```cpp
    struct Win32_MyCustomClass
    {
    
        /**
          * This function is called by requestWmi and requestAllWmi
          * with the actual WmiResult
         **/
        void setProperties(const WmiResult &result, std::size_t index)
        {
            //EXAMPLE EXTRACTING PROPERTY TO CLASS
		    result.extract(index, "name", (*this).name);
        }
    
        /**
          * This function is used to determine the actual WMI class name
         **/
        static std::string getWmiClassName()
        {
            return "Win32_MyCustomClass";
        }
    
        string name;
        //All the other properties you wish to read from WMI
    
    }; //end struct Win32_ComputerSystem
```
