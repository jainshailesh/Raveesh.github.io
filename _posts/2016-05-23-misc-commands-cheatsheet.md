---
layout: post
title: "Miscellaneous Commands"
date: 2016-05-19
---

# Miscellaneous Commands

## Programming
### Creating a maven parent project and sub projects using command line 
 
`mvn archetype:generate -DgroupId=com.sample.package -DartifactId=sample-project`
Ensure the  `<module>` tag exists in the parent pom
Once the parent project is created, `cd` into the parent project and repeat the above command. 

### Building Spark with scala 2.11
* Run `./dev/change-scala-version.sh 2.11`
* Run `mvn -Pyarn -Phadoop-2.4 -Dscala-2.11 -DskipTests clean package`


## Linux Ubuntu
### Resetting Ubuntu theme

* Reset Icon Pack `gsettings set org.gnome.desktop.interface icon-theme ''`
* Reset Theme 
  1. `gsettings set org.gnome.desktop.interface gtk-theme ''`
  2. `gsettings set org.gnome.desktop.wm.preferences theme ''`
  3. `gconftool-2 --set --type string /apps/metacity/general/theme ''`
* Reset Launcher `gsettings reset com.canonical.Unity.Launcher favorites`
* Reset Panel `gsettings set com.canonical.Unity.Panel systray-whitelist "['all']" `
Log out and Login again. 

## Initializing Log4j in spring applications

Add the following snippet to your application-context.xml
```
<bean id="log4jInitialization"
 class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
   <property name="targetClass"
      value="org.springframework.util.Log4jConfigurer" />
   <property name="targetMethod" value="initLogging" />
   <property name="arguments">
      <list>
         <value>conf/log4j.xml</value>
      </list>
   </property>
</bean>
```

## Obtaining the serial id of a Linux based machine
``` $ > dmidecode -s system-serial-number  ```

## Installing Python 2.7.10 on a new VM 
``` 
sudo apt-get install -y gcc-multilib g++-multilib libffi-dev libffi6 libffi6-dbg python-crypto python-mox3 python-pil python-ply libssl-dev zlib1g-dev libbz2-dev libexpat1-dev libbluetooth-dev libgdbm-dev dpkg-dev quilt autotools-dev libreadline-dev libtinfo-dev libncursesw5-dev tk-dev blt-dev libssl-dev zlib1g-dev libbz2-dev libexpat1-dev libbluetooth-dev libsqlite3-dev libgpm2 mime-support netbase net-tools bzip2

$ wget https://www.python.org/ftp/python/2.7.10/Python-2.7.10.tgz
$ tar xvf Python-2.7.10.tgz
$ cd Python-2.7.10/

$ ./configure --prefix /usr/local/lib/python2.7.10 --enable-ipv6
$ make
$ sudo make install

$ /usr/local/lib/python2.7.10/bin/python -V
Python 2.7.10 
```

## Generic conversion of json String to the class desired. 

```
  public <T> T unmarshall(String jsonString, TypeReference<T> type) {
    ObjectMapper mapper = new ObjectMapper();
    mapper.setSerializationInclusion(Include.NON_NULL);
    try {
      if (jsonString != null) {
        T object = mapper.readValue(jsonString, type);
        return object;
      }
    } catch (IOException e) {
      System.err.println("JSON deserialization exception" + e);
    }
    return null;
  }
```
