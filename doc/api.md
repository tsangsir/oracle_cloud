# Command Line Utility

> **Warning:** `oci` is not convenient for automation. It should be used for 
manual ad hoc usage only.

## Installation

```
bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
```

Didn't install DB module on `imtsangsir-arm-ubuntu` because `cx_oracle` compilation failure (and cx_oracle is obsolete)

## Configuration

(on `imtsangsir-arm-ubuntu`)

```
ubuntu@imtsangsir-arm-ubuntu:~/bin$ ./oci setup config
...
Enter a location for your config [/home/ubuntu/.oci/config]:  
Enter a user OCID: ocid1.user.oc1..aaaaaaaaidkysisrecvrr4nahnu456bbhevtn6vn2sxwqomntnjgmcqlta3q
Enter a tenancy OCID: ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga
Enter a region by index or name(e.g. ...50: uk-london-1...): 50
Do you want to generate a new API Signing RSA key pair? (If you decline you will be asked to supply the path to an existing key.) [Y/n]: y
Enter a directory for your keys to be created [/home/ubuntu/.oci]:  
Enter a name for your key [oci_api_key]:  
Public key written to: /home/ubuntu/.oci/oci_api_key_public.pem
Enter a passphrase for your private key ("N/A" for no passphrase):  
Repeat for confirmation:  
Private key written to: /home/ubuntu/.oci/oci_api_key.pem
Fingerprint: 22:dc:86:12:a0:21:82:85:90:1c:e3:72:06:40:00:db
Config written to /home/ubuntu/.oci/config
...
```

Output:

```
[DEFAULT]
user=ocid1.user.oc1..aaaaaaaaidkysisrecvrr4nahnu456bbhevtn6vn2sxwqomntnjgmcqlta3q
fingerprint=22:dc:86:12:a0:21:82:85:90:1c:e3:72:06:40:00:db
key_file=/home/tathong/.oci/oci_api_key.pem
tenancy=ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga
region=uk-london-1
```
Upload the API Key in OCI Cloud Console: Identity -> My Profile -> API Keys

## Examples

### List Compartments 

(This ended up not useful, because I've left everything in root compartment, which is same as the tenacy)

```
$ ./oci iam compartment list  
{
 "data": [
   {
     "compartment-id": "ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga",
     "defined-tags": {},
     "description": "idcs-d522f3e55b2f422d93ecf652716c888b|23287375|imtsangsir@gmail.com-N/A-463131",
     "freeform-tags": {},
     "id": "ocid1.compartment.oc1..aaaaaaaalrxau6qwntbjlmarwewtlq25653tt25qqtd4a4mjtnqeq4ewggpq",
     "inactive-status": null,
     "is-accessible": null,
     "lifecycle-state": "ACTIVE",
     "name": "ManagedCompartmentForPaaS",
     "time-created": "2020-11-24T14:46:33.465000+00:00"
   }
 ]
}
```

### List all ADB

```
./oci db autonomous-database list --compartment-id ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga

...
       "whitelisted-ips": [
       "94.197.251.105",
       "ocid1.vcn.oc1.uk-london-1.amaaaaaax4xsifaavf2jaxdkp7lipo4pl4zd4wal4mbz4otus7f7tksbbuvq",
       "150.230.122.135"
     ]
   }
 ]
}
```

### Configure Network ACL for ADB

```
ubuntu@imtsangsir-arm-ubuntu:~/bin$ who
ubuntu   pts/0        2024-02-27 00:22 (94.197.237.82)


ubuntu@imtsangsir-arm-ubuntu:~/bin$ oci db autonomous-database update  --autonomous-database-id  ocid1.autonomousdatabase.oc1.uk-london-1.anwgiljsx4xsifaa4ww6ysztqk5xlk3wek5wo5eg2fzildsf6libu455vuwa --whitelisted-ips '
[
"94.197.251.105",       "ocid1.vcn.oc1.uk-london-1.amaaaaaax4xsifaavf2jaxdkp7lipo4pl4zd4wal4mbz4otus7f7tksbbuvq",
"150.230.122.135",
"94.197.237.82"
]'
```

# Python

## References
https://docs.oracle.com/en-us/iaas/tools/python-sdk-examples/2.122.0/database/update_autonomous_database.py.html

https://docs.oracle.com/en-us/iaas/tools/python/2.122.0/api/database/client/oci.database.DatabaseClient.html#oci.database.DatabaseClient.update_autonomous_database

https://github.com/oracle/oci-python-sdk/blob/master/examples/database/adb_example.py (line 102)

https://raw.githubusercontent.com/oracle/oci-cli/v3.2.1/scripts/install/install.py


## Installation

```
$ pip install oci
```

## List Compartments

```
import oci
config = oci.config.from_file(profile_name='tsangsir')
database_client = oci.database.DatabaseClient(config)

identity_client = oci.identity.IdentityClient(config)
list_compartments_response = identity_client.list_compartments(
   compartment_id="ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga"
)
print(list_compartments_response.data)
```

Output

```
[{
 "compartment_id": "ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga",
 "defined_tags": {},
 "description": "idcs-d522f3e55b2f422d93ecf652716c888b|23287375|imtsangsir@gmail.com-N/A-463131",
 "freeform_tags": {},
 "id": "ocid1.compartment.oc1..aaaaaaaalrxau6qwntbjlmarwewtlq25653tt25qqtd4a4mjtnqeq4ewggpq",
 "inactive_status": null,
 "is_accessible": null,
 "lifecycle_state": "ACTIVE",
 "name": "ManagedCompartmentForPaaS",
 "time_created": "2020-11-24T14:46:33.465000+00:00"
}]
```

## List ADB and Whitelisted IPs

```
list_autonomous_databases_response = database_client.list_autonomous_databases(
   compartment_id="ocid1.tenancy.oc1..aaaaaaaadvddhmlsiapbjzr75lovskp2bymowfmrqbr6kgbdvg3l7ixn5qga"
)
print(list_autonomous_databases_response.data[0].whitelisted_ips)
```

Output:

```
[
   "94.197.251.105",
   "ocid1.vcn.oc1.uk-london-1.amaaaaaax4xsifaavf2jaxdkp7lipo4pl4zd4wal4mbz4otus7f7tksbbuvq",
   "94.197.237.82",
   "150.230.122.135"
 ]
```

