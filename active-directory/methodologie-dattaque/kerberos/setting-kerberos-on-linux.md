# Setting Kerberos on Linux

* Modify your **/etc/hosts** file so that you'll be able to use domain name instead of IP in your commands
* Install the kerberos client `sudo apt install krb5-user`
*   Setup the **/etc/krb5.conf** file&#x20;

    ```
    [libdefaults]
      default_realm = essos.local
      kdc_timesync = 1
      ccache_type = 4
      forwardable = true
      proxiable = true
      fcc-mit-ticketflags = true
    [realms]
      north.sevenkingdoms.local = {
          kdc = winterfell.north.sevenkingdoms.local
          admin_server = winterfell.north.sevenkingdoms.local
      }
      sevenkingdoms.local = {
          kdc = kingslanding.sevenkingdoms.local
          admin_server = kingslanding.sevenkingdoms.local
      }
      essos.local = {
          kdc = meereen.essos.local
          admin_server = meereen.essos.local
      }
    ...
    ```
