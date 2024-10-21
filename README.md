# DNS Practice with Vagrant in sistema.test

This project sets up a master and slave DNS server configuration using virtual machines managed by Vagrant. The goal is to create a direct and reverse DNS zone in a simulated network (`192.168.57.0/24`), including validation of DNS records and zone transfers.

## Network Topology

All machines in the network (both real and virtual) will be in the `192.168.57.0/24` subnet. The machines are configured as follows:

| Machine            | FQDN                 | IP Address         |
|--------------------|----------------------|--------------------|
| Graphical Linux VM | `mercurio.sistema.test` | `192.168.57.101`   |
| Text-based Debian  | `venus.sistema.test`    | `192.168.57.102`   |
| Text-based Debian  | `tierra.sistema.test`   | `192.168.57.103`   |
| Windows Server (imaginary) | `marte.sistema.test`   | `192.168.57.104`   |

- The **master DNS server** will be `tierra.sistema.test` (`192.168.57.103`).
- The **slave DNS server** will be `venus.sistema.test` (`192.168.57.102`).
- `marte.sistema.test` (`192.168.57.104`) will act as the mail server for the domain `sistema.test`.

## Configuration Files

The repository includes several configuration files:

1. **Vagrantfile**: Sets up the virtual machines used in the project.
2. **DNS Configuration Files**: These files configure the master and slave DNS servers, zone settings, ACLs, and other necessary options.
3. **README.md**: This document explains how to run and validate the DNS setup.
4. **LICENSE**: This project is licensed under [GNU General Public License v3.0](LICENSE).

### DNS Settings:

- Enable the server to listen only on IPv4.
- Set `dnssec-validation` to `yes`.
- The servers will allow recursive queries only for computers in the `127.0.0.0/8` and `192.168.57.0/24` networks using Access Control Lists (ACL).
- Negative cache response time for the zones is set to two hours (in seconds).
- Queries for which the server is not authoritative will be forwarded to `208.67.222.222` (OpenDNS).

Aliases:
- `ns1.sistema.test` is an alias for `tierra.sistema.test`.
- `ns2.sistema.test` is an alias for `venus.sistema.test`.
- `mail.sistema.test` is an alias for `marte.sistema.test`.

## Prerequisites

Before starting, ensure you have the following installed on your system:

- [Vagrant](https://www.vagrantup.com/downloads)
- [VirtualBox](https://www.virtualbox.org/) (or another virtualization provider compatible with Vagrant)
- [Git](https://git-scm.com/)

## Installation Instructions

### 1. Clone the repository

First, clone this repository to your local machine:

```bash
git clone https://github.com/egenerei/assignment_3_dns_server_master_slave.git
cd assignment_3_dns_server_master_slave
```

### 2. Bring up the virtual machines

Inside the project directory, use Vagrant to create and configure the virtual machines for the DNS servers:

```bash
vagrant up
```

This will download the virtual machine images and automatically set up the master DNS server on `tierra.sistema.test` (192.168.57.103) and the slave DNS server on `venus.sistema.test` (192.168.57.102).

### 3. Verify machine status

Once the machines are running, verify that they are up and running correctly:

```bash
vagrant status
```

You should see that both `tierra` and `venus` are in a `running` state.

## Running the Practice

### 1. Verify name resolution

With the virtual machines running, you can use `dig` or `nslookup` to test name resolution. Below are some useful commands:

- Query the master server (`tierra.sistema.test`):
    ```bash
    dig @192.168.57.103 sistema.test
    ```

- Query the slave server (`venus.sistema.test`):
    ```bash
    dig @192.168.57.102 sistema.test
    ```

### 2. Verify zone transfer

Zone transfer is a key step in the setup. To verify that the zone transfer has occurred successfully between the master and the slave, you can run:

```bash
dig @192.168.57.102 AXFR sistema.test
```

This command should return all DNS records of the zone transferred from the master to the slave.

### 3. Query specific records

You can also query other types of DNS records, such as NS and MX:

- NS Records:
    ```bash
    dig @192.168.57.103 NS sistema.test
    ```

- MX Records:
    ```bash
    dig @192.168.57.103 MX sistema.test
    ```

Additionally, you can check the configured aliases for the DNS servers:

- Alias `ns1.sistema.test`:
    ```bash
    dig @192.168.57.103 ns1.sistema.test
    ```

- Alias `ns2.sistema.test`:
    ```bash
    dig @192.168.57.102 ns2.sistema.test
    ```

### 4. Validate reverse resolution

Make sure that IP addresses are correctly resolved to domain names:

```bash
dig @192.168.57.103 -x 192.168.57.103
```

This command should return `tierra.sistema.test` as the result.

## Shutting Down the Machines

When you're done with the virtual machines, you can stop and destroy them if needed:

- To stop the machines without destroying them:
    ```bash
    vagrant halt
    ```

- To destroy the machines and free up disk space:
    ```bash
    vagrant destroy
    ```

## Common Issues

1. **DNS connection error**: Verify that the virtual machines are running (`vagrant status`) and that the IPs are accessible from your host machine.
   
2. **Permission issues**: Ensure that you are running Vagrant with the appropriate permissions and that your firewall is not blocking network connections between your host machine and the virtual machines.

## License

This project is available under the [GNU General Public License v3.0](LICENSE).