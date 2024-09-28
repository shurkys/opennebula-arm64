# Packages and Docker for OpenNebula on ARM

This repository provides easy access to .deb packages and Docker examples for running OpenNebula on ARM architecture.

## Getting Started

### Install .deb Packages

You can download the latest .deb packages from the following link:

[OpenNebula ARM Releases](https://github.com/shurkys/opennebula-arm64/releases/)

### Running with Docker

To run OpenNebula using Docker, execute the following command on your Mac with an M series CPU or on any ARM PC (such as an Armbian TV BOX, Orange Pi, Raspberry Pi, or a VM):

```bash
docker-compose up
# Get login/password
docker exec -ti opennebula cat .one/one_auth
```
## Notes

- The frontend works quite well. You can separate it into different components and add MySQL and Memcached nodes. Consider implementing persistent storage for images and other resources.
- KVM node may not function correctly on ~~some~~ all setups.
- LXC support appears more promising.

## Known Issues

1. VCPU Pinning: ARM, at least with the ones I've worked with, does not support VCPU Pinning; and it is hardcoded. [Link to code](https://github.com/OpenNebula/one/blob/master/src/vmm/LibVirtDriverKVM.cc#L785)
2. VM Configuration: You need to manually edit or add the following configuration for starting ARM VMs:
```plaintext
OS=[
  ARCH="aarch64"
]
```
