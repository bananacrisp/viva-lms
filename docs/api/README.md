# Viva LMS API Documentation

This directory contains comprehensive documentation for all API endpoints in the Viva Learning Management System.

## API Groups

### 00_user_auth
User authentication and management endpoints.

### 02_content_management
Subject and content management endpoints.

### 03_cima_objective_test_exams
CIMA objective test exam generation and management.

- [Generate Exam Optimised](./cima-objective-tests/generate-exam-optimised.md) - Optimized exam generation endpoint

### 04_cima_case_study_exams
CIMA case study exam endpoints.

### 05_acca_exams
ACCA exam endpoints (coming soon).

### 06_results_analytics
Results and analytics endpoints (coming soon).

## API Base URL

Production: `https://xano.vivatuition.com`  
Development: `https://xj81-ukxc-jagn.e2.xano.io`

## Authentication

Most endpoints require authentication using a bearer token obtained from the `/swap_token` endpoint.