
# juju charm

Run juju inside of juju.

This is a charm to install and manage a juju client.  Use this
to run the juju cli against environments passed as config.

As a subordinate, this can enable a juju cli for any service.
This is typically useful as a subordinate to jenkins-slave for
charmtesting or to turn byobu-classroom into juju-classroom.

As a standalone charm, this can be useful when experimenting
with different environments/providers.


# Examples

## Simple juju-classroom

    laptop$ juju bootstrap
    laptop$ juju deploy byobu-classroom juju-classroom
    laptop$ juju deploy juju juju-client
    laptop$ juju add-relation juju-classroom juju-client

    laptop$ juju ssh juju-classroom/0
    <ec2-addr>$ echo "Ok, folks... now watch me demo juju using the local provider"
    <ec2-addr>$ juju bootstrap
    <ec2-addr>$ juju deploy mysql
    <ec2-addr>$ juju status

## juju-classroom using other environments

Write the following to ~/juju-classroom-ec2.yaml

    juju-client:
      juju_environment: |
        default: ec2
        environments:
          local:
            type: local
            data-dir: /var/lib/jenkins/.juju/local-precise
            admin-secret: 0abcdefghijklmnopqrstuvwxyz123456789
            control-bucket: local-precise-bucket
            default-series: precise
          ec2:
            type: ec2
            admin-secret: <admin-secret-hash>
            control-bucket: <juju-classroom-bucketname>
            access-key: <ec2 access key>
            secret-key: <ec2 secret key>
            default-series: precise

and then change the simple deployment above to use this config:

    laptop$ juju deploy --config ~/juju-classroom-ec2.yaml juju juju-client

now the service's juju cli will run against the new default environment


# TODO

