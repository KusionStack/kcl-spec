# Introduction

Traditionally, cloud native application deployment is performed in the
Infrastructure-as-Data (IaD) paradigms. For example,
Kubernetes-applications are operated with YAML description files
supplied.

The K Configuration Language, abbreviated as KCL, offers a solution to
operate infrastructures as code, i.e., provides support to the
Infrastructure-as-Code (IaC) paradigms.

With KCL, configurations can be developed into source code in KCL, and a
KCL execution engine executes the KCL code and generates YAML files that
can be supplied to other applications such as Kubernetes.

KCL resembles the Python Programming Language in terms of syntax and
built-in modules. However, as a configuration language, it has many
fundamental differences.

## Terms and Definitions

In this specification, the KCL grammars are described in the Backus–Naur
Form (BNF).
