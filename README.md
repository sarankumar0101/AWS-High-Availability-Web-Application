# AWS High Availability Web Application

A hands-on AWS project demonstrating a custom-domain web application hosted on two Amazon EC2 instances, fronted by an Application Load Balancer (ALB), with DNS managed through Amazon Route 53 and HTTPS enabled using AWS Certificate Manager (ACM).

## Project highlights

- Two Amazon EC2 instances running Nginx and serving different web pages.
- Application Load Balancer distributes requests across the registered targets.
- Target group health checks determine which EC2 instances can receive traffic.
- Amazon Route 53 Alias A record maps the custom domain to the ALB.
- ACM public certificate for `devops-hub.online` is attached to the ALB HTTPS listener in Europe (Stockholm), `eu-north-1`.
- HTTP requests on port 80 redirect to HTTPS on port 443.

## Architecture / request flow

1. A visitor opens `https://devops-hub.online`.
2. Route 53 resolves the domain to the Application Load Balancer.
3. The ALB terminates TLS using the ACM certificate on HTTPS listener port 443.
4. The ALB forwards the request to the target group.
5. The target group routes the request to a healthy EC2 Nginx web server.
6. Requests made over HTTP are redirected to HTTPS.

## AWS services and components

- Amazon VPC, subnets, route tables, Internet Gateway and NAT Gateway
- Security Groups
- Amazon EC2
- Application Load Balancer and Target Group
- AWS Certificate Manager (ACM)
- Amazon Route 53
- Nginx

## Repository contents

Project documentation and implementation screenshots will be added under the `documentation/` and `screenshots/` directories.

## Testing

The completed setup was tested through the custom domain. The browser showed a secure HTTPS connection, HTTP requests redirected to HTTPS, and refreshing the page showed responses from the two different EC2 web servers.

## Notes

This repository documents a learning project. Do not commit AWS access keys, private keys, passwords, database credentials, or other secrets. Verify all security-group rules and remove unused AWS resources to avoid ongoing charges.
