# The Kusion Configuration Language Specification

## About this Documentation

The Kusion Configuration Language (KCL) is a language designed to enable
the Infrastructure-as-Code (IaC) paradigms in Cloud-Native application
deployment.

This document is a specification to the Kusion Configuration Language.

## Table of Contents

Contents:

- Introduction
  - Terms and Definitions
- Preview
  - Hello, World!
  - Simple YAML Generation
  - Variables are Constants
  - Schema
    - Default Value
    - Validation Failure
    - Other Things a Schema Can Do
- Lexical Conventions
  - Grammar Notation
  - Source File Encoding
  - Line Structure
    - Explicit Line Joining
    - Implicit Line Joining
    - Blank Lines
    - Indentation
  - Comments
  - Identifiers and Keywords
    - Keywords
  - Literals
    - String Literals
    - Numeric Literals
  - Operators and Delimiters
    - Operators
    - Delimiters
- Variables
  - Immutability
  - Variable Exporting
  - Uniqueness of exported variable identifier
- Modules and the Import System
  - Packages
    - Intra-Package Name Space Sharing
    - Package Initialization
  - Searching
    - Module Cache
    - Module Names
    - Uniqueness of module
  - Standard System Modules
    - The Built-in System Module
    - Plugin Modules
    - Replacing Standard System Modules
  - Examples
    - Importing a Standard System Module
    - Importing a Regular Module
    - Importing a Package
    - Importing a Subpackage
    - Relative Importing
    - Importing from a Root Path
    - Importing a Module Inside a Package
    - Precedence of Importing
    - Package Implemented with Multiple Files
