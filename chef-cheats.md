# Chef Cheats
The chef product stack has many many components, so this will likely because a deep and long laundry list.

## Knife - Security
Administrating security settings or navigating the chef server security model can be very confusing.  Here are some cheats that I use regularly.

Several of these cheats use knife-acl which is a gem not shipped w/ the ChefDK, but provides knife commands into the security model that are vital and not equalled anywhere else.  It should be included in the ChefDK, but it's not, so ensure you have it installed with:

```
chef gem install knife-acl
```

### View the members of a group
```
$ knife group show worker
actors:
  cloud_provisioner
  nodes_manager
clients:
groupname: worker
groups:
name:      worker
orgname:   foo
users:
  cloud_provisioner
  nodes_manager
```

### View the permissions on a group
This is the thing that always always trips me up.  You create the group by defining it's name and it's permissions by object.  You would think there's a way to see the group and the permissions it has per object, but no.  You must 'show' each object container individually to get an understanding of which group has what permissions
```
knife acl show containers cookbooks
knife acl show containers policies
knife acl show containers policy_groups
```

### Add group permission
```
knife acl add group publisher containers policy_groups create
```
### Remove group permissions
```
knife acl remove group publisher containers policy_groups create
```
