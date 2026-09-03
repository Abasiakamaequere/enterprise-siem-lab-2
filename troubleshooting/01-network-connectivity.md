# Troubleshooting 01 — Network Connectivity

## Problem

The initial Splunk package download from the Ubuntu Server encountered IPv6 connectivity problems.

## Symptoms

The download attempt failed because the system was unable to establish a working IPv6 connection to the remote package source.

## Diagnosis

Network connectivity was tested and the failure was isolated to IPv6 connectivity rather than the overall network configuration.

## Resolution

The download was forced over IPv4 using:

`wget -4`

This allowed the package download to proceed using the available IPv4 connection.

## Engineering Context

The issue demonstrated the importance of distinguishing between general network connectivity and protocol-specific connectivity.

Rather than repeatedly retrying the same failed download, the network path was diagnosed and the download method was adjusted to use the working protocol.

## Outcome

The Splunk package was successfully obtained after forcing IPv4 connectivity.

## Lessons Learned

* IPv6 connectivity should not be assumed to be functional simply because the system has network access.
* Network failures should be isolated before changing configuration.
* Forcing IPv4 can provide a practical workaround when IPv6 connectivity is unavailable.
* Troubleshooting decisions should be based on observed behavior rather than assumptions.
