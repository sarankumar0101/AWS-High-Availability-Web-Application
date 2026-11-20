# Project Documentation: AWS High Availability Web Application

## 1. Project Overview

This project demonstrates a web application hosted on two Amazon EC2 instances and exposed through an Application Load Balancer (ALB). The custom domain `devops-hub.online` is mapped to the ALB using Amazon Route 53. HTTPS is configured on the ALB with an Amazon-issued AWS Certificate Manager (ACM) certificate in Europe (Stockholm), `eu-north-1`.

Each EC2 instance runs Nginx and serves a different page so that requests reaching different targets can be identified.

## 2. Objectives

- Build the required AWS network using a VPC, subnets, route tables, an Internet Gateway and a NAT Gateway.
- Deploy two EC2 web servers running Nginx.
- Register both EC2 instances in an ALB target group.
- Route the custom domain to the ALB using Route 53.
- Enable HTTPS on the ALB using an ACM certificate.
- Redirect HTTP requests to HTTPS.

## 3. Architecture and Request Flow

1. A visitor opens `https://devops-hub.online`.
2. Route 53 resolves the domain through an Alias A record pointing to the ALB.
3. The ALB accepts the HTTPS connection on port 443 and presents the ACM certificate.
4. The ALB forwards the request to its target group.
5. The target group sends the request to a healthy registered EC2 instance.
6. The EC2 instance's Nginx server returns its web page.
7. Requests arriving over HTTP on port 80 are redirected to HTTPS.

## 4. AWS Services Used

| Service / component | Role |
|---|---|
| Amazon VPC | Network boundary for project resources |
| Subnets | Network segmentation |
| Internet Gateway | Internet connectivity for routes using the gateway |
| NAT Gateway | Outbound internet access for private subnet resources when routed through it |
| Route Tables | Control subnet traffic routing |
| Security Groups | Stateful network access controls |
| Amazon EC2 | Hosts the two Nginx web servers |
| Target Group | Registers EC2 targets and performs health checks |
| Application Load Balancer | Receives application traffic and distributes it to healthy targets |
| AWS Certificate Manager | Supplies the TLS certificate used by the ALB HTTPS listener |
| Amazon Route 53 | DNS hosting and domain-to-ALB routing |
| Nginx | Serves the web pages on the EC2 instances |

## 5. Network Configuration

An Amazon VPC was created for the project. Subnets, route tables, an Internet Gateway and a NAT Gateway were configured to provide the required network paths. Security groups were configured to control inbound and outbound traffic.

Refer to the project screenshots for the exact VPC, subnet, route-table, gateway and security-group configuration. Exact CIDR ranges and subnet-to-Availability-Zone mappings should be recorded from the AWS console rather than assumed.

## 6. EC2 Web Server Deployment

Two EC2 instances were launched and configured with Nginx. Each instance serves a distinct HTML page:

- Server 1 displays a “Welcome to Server 1” page.
- Server 2 displays a “Welcome to Server 2” page.

Refreshing the website can show which backend instance responded to a request.

## 7. Application Load Balancer

An Application Load Balancer was created in the project VPC. A target group was configured and the two EC2 instances were registered as targets.

The ALB listeners configured for the project are:

- **HTTP : 80** — redirects requests to HTTPS.
- **HTTPS : 443** — uses the ACM certificate and forwards requests to the target group.

The target group's health checks determine which registered instances are eligible to receive traffic.

## 8. Custom Domain and ACM Certificate

The custom domain is `devops-hub.online`. Its Route 53 hosted zone contains an Alias A record that routes the apex domain to the Application Load Balancer.

An Amazon-issued public ACM certificate for `devops-hub.online` was DNS validated and reached the **Issued** state in Europe (Stockholm), region `eu-north-1`. The certificate is associated with the ALB HTTPS listener.

> Note: An ACM certificate used directly by an Application Load Balancer must be in the same AWS Region as that load balancer. CloudFront has a separate certificate-region requirement; this project uses the ALB for HTTPS termination.

## 9. Testing and Results

The completed setup was tested using the custom domain:

- The browser displayed a secure connection for the website.
- HTTP requests were redirected to HTTPS.
- The two distinct server pages were observed when refreshing the website, demonstrating responses from the EC2 backends through the load balancer.

## 10. Security and Operational Considerations

- Allow inbound HTTPS (TCP 443) to the ALB from intended clients.
- Where practical, allow the EC2 web-server port only from the ALB security group.
- Keep the ALB listener, target-group protocol and instance configuration consistent.
- Monitor target health and ALB metrics.
- Never commit AWS access keys, private keys, passwords, database credentials or other secrets to the repository.
- Review and delete unused AWS resources to avoid unnecessary charges.

## 11. Project Outcome

The project demonstrates a custom-domain web deployment on AWS using two EC2-based Nginx servers, an Application Load Balancer, Route 53 DNS and an ACM-managed HTTPS certificate. The final browser tests confirmed secure access, HTTP-to-HTTPS redirection and responses from both server pages.

## 12. Scope Notes

This documentation is based on the project details and supplied screenshots. The available evidence does not establish exact CIDR ranges, instance types, all security-group rule values, or a completed application-to-database integration. Those details should only be added after verification in the AWS console.
