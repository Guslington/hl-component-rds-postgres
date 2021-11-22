# rds-postgres CfHighlander component

## Parameters

| Name | Use | Default | Global | Type | Allowed Values |
| ---- | --- | ------- | ------ | ---- | -------------- |
| EnvironmentName | Tagging | dev | true | string
| EnvironmentType | Tagging | development | true | string | ['development','production']
| SubnetIds | List of subnets | None | false | CommaDelimitedList
| RDSInstanceType | RDS Instance Type to use | None | false | string
| RDSAllocatedStorage | Amount of storage in GB to assign | None | false | string
| DnsDomain | DNS domain to use | None | true | string
| MultiAZ | Specifies whether the database instance is a multiple Availability Zone deployment  | false | false | ['true','false']
| RDSSnapshotID | The name or Amazon Resource Name (ARN) of the DB snapshot that's used to restore the DB instance | false | false | string

## Outputs/Exports

| Name | Value | Exported |
| ---- | ----- | -------- |
| SecurityGroup | Security Group Name | true
| DBEndpoint | RDS Endpoint.Address value | true
| DBPort | RDS Endpoint.Port | true

## Included Components

[lib-ec2](https://github.com/theonestack/hl-component-lib-ec2)

## Example Configuration
### Highlander
```
  Component name:'database', template: 'rds-postgres' do
    parameter name: 'DnsDomain', value: root_domain
    parameter name: 'DnsFormat', value: FnSub("${EnvironmentName}.#{root_domain}")
    parameter name: 'SubnetIds', value: cfout('vpcv2', 'PersistenceSubnets')
    parameter name: 'RDSInstanceType', value: 'db.m5.large'
    parameter name: 'MultiAZ', value: 'false'
    parameter name: 'StackOctet', value: '120'
    parameter name: 'RDSAllocatedStorage', value: '100'
  end

```

### RDS-POSTGRES Configuration

```
db_name: myappdb
engine: postgres
engine_version: '13.4'
family: 'postgres13'
dns_record: db
deletion_policy: Snapshot

storage_encrypted: true
maintenance_window: sat:18:00-sat:18:30

security_group:
  -
    rules:
      -
        IpProtocol: tcp
        FromPort: 5432
        ToPort: 5432
    ips:
      - stack

master_login:
  username_ssm_param: /project/rds/RDS_MASTER_USERNAME
  password_ssm_param: /project/rds/RDS_MASTER_PASSWORD

```

## Cfhighlander Setup

install cfhighlander [gem](https://github.com/theonestack/cfhighlander)

```bash
gem install cfhighlander
```

or via docker

```bash
docker pull theonestack/cfhighlander
```
## Testing Components

Running the tests

```bash
cfhighlander cftest rds-postgres
```