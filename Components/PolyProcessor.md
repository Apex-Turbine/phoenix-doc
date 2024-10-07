# PolyProcessor

## Description

Applies a polynomial transform to incoming messages on an element-wise basis.

## I/O

Receives numeric vector compatible data.

Produces single precision vectors containing polynomial mapped result.

## JSON Setup Keys

This component follows standard input keys:
- name
- units
- streamid
- sourcename
- size

Component specific input keys:
- poly
	- Vector of coefficients starting from the highest order
	- No default, **Must** be present or channel will be ignored

[Back](PhoenixComponents.md)

