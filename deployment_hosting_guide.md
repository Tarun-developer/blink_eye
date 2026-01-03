# Blink Eye Hospitals Platform: Deployment and Hosting Guide

## Introduction

This guide provides comprehensive instructions for deploying and hosting the Blink Eye Hospitals platform, a multi-tenant healthcare management system. The platform follows a microservices architecture with tenant isolation, as detailed in the [architecture diagrams](architecture_diagrams.md).

Key architectural components include:
- **Multi-tenant isolation** via subdomain-based routing and PostgreSQL Row-Level Security (RLS)
- **Microservices** for modular functionality (EHR, appointments, billing, etc.)
- **API Gateway** for authentication, routing, and load balancing
- **PostgreSQL database** with tenant-specific data partitioning
- **Redis cache** for session and data caching
- **File storage** for documents and images

The deployment targets AWS infrastructure with Cloudflare for CDN and security.

## Prerequisites

Before deployment, ensure the following:

### System Requirements
- AWS account with appropriate permissions
- Cloudflare account for DNS and CDN
- Docker and Docker Compose for local development
- Node.js 18+ and Python 3.9+ for application runtime
- PostgreSQL 13+ and Redis 6+ for data services

### Knowledge Requirements
- AWS services (EC2, RDS, ElastiCache, S3, CloudFront)
- Docker containerization
- CI/CD pipelines (GitHub Actions recommended)
- Security best practices for healthcare data (HIPAA compliance)
- Multi-tenant application deployment

### Required Accounts and Access
- AWS IAM user with AdministratorAccess or equivalent
- Cloudflare API token with Zone and DNS edit permissions
- GitHub repository access for CI/CD
- SSL certificates (Let's Encrypt or purchased)

## Infrastructure Setup (AWS/Cloudflare)

### Step 1: AWS VPC and Networking

1. Create a VPC with public and private subnets across multiple availability zones:
   ```bash
   aws ec2 create-vpc --cidr-block 10.0.0.0/16 --region us-east-1
   aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.0.1.0/24 --availability-zone us-east-1a
   aws ec2 create-subnet --vpc-id vpc-12345678 --cidr-block 10.0.2.0/24 --availability-zone us-east-1b
   ```

2. Set up Internet Gateway and NAT Gateway for outbound traffic.

3. Configure security groups for application, database, and cache access.

### Step 2: Database Provisioning (AWS RDS)

1. Launch PostgreSQL RDS instance:
   ```bash
   aws rds create-db-instance \
     --db-instance-identifier blink-eye-db \
     --db-instance-class db.r5.large \
     --engine postgres \
     --engine-version 13.7 \
     --master-username blink_admin \
     --master-user-password <secure-password> \
     --allocated-storage 100 \
     --vpc-security-group-ids sg-12345678 \
     --db-subnet-group-name blink-eye-db-subnet
   ```

2. Enable Multi-AZ deployment for high availability.

3. Configure automated backups and maintenance windows.

### Step 3: Redis Cache (AWS ElastiCache)

1. Create Redis cluster:
   ```bash
   aws elasticache create-cache-cluster \
     --cache-cluster-id blink-eye-cache \
     --cache-node-type cache.r5.large \
     --engine redis \
     --engine-version 6.2 \
     --num-cache-nodes 2 \
     --cache-subnet-group-name blink-eye-cache-subnet
   ```

### Step 4: File Storage (AWS S3)

1. Create S3 buckets for each environment:
   ```bash
   aws s3 mb s3://blink-eye-prod-documents --region us-east-1
   aws s3 mb s3://blink-eye-prod-images --region us-east-1
   ```

2. Enable versioning and server-side encryption.

3. Configure CORS for web access.

### Step 5: Application Hosting (AWS ECS/EKS)

1. Set up ECS cluster or EKS cluster for containerized deployment.

2. Create ECR repositories for microservices:
   ```bash
   aws ecr create-repository --repository-name blink-eye/api-gateway
   aws ecr create-repository --repository-name blink-eye/ehr-service
   aws ecr create-repository --repository-name blink-eye/appointment-service
   ```

### Step 6: Cloudflare Configuration

1. Add domain to Cloudflare and update nameservers.

2. Configure DNS records:
   - A record for main domain pointing to CloudFront distribution
   - CNAME records for tenant subdomains

3. Set up SSL/TLS with Full (strict) encryption.

4. Configure WAF rules for healthcare-specific security.

5. Set up CDN caching rules for static assets.

## CI/CD Pipelines

### GitHub Actions Setup

1. Create `.github/workflows/deploy.yml`:
   ```yaml
   name: Deploy to Production
   on:
     push:
       branches: [ main ]
   jobs:
     build-and-deploy:
       runs-on: ubuntu-latest
       steps:
       - uses: actions/checkout@v3
       - name: Build and push Docker images
         run: |
           aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account>.dkr.ecr.us-east-1.amazonaws.com
           docker build -t blink-eye/api-gateway .
           docker tag blink-eye/api-gateway:latest <account>.dkr.ecr.us-east-1.amazonaws.com/blink-eye/api-gateway:latest
           docker push <account>.dkr.ecr.us-east-1.amazonaws.com/blink-eye/api-gateway:latest
       - name: Deploy to ECS
         uses: aws-actions/amazon-ecs-deploy-task-definition@v1
         with:
           task-definition: task-definition.json
           service: blink-eye-service
           cluster: blink-eye-cluster
   ```

2. Configure environment secrets in GitHub repository.

### Multi-Environment Deployment

1. Set up separate environments (dev, staging, prod).

2. Use branch protection rules for production deployments.

3. Implement blue-green deployment strategy for zero-downtime updates.

## Database Provisioning

### Initial Schema Setup

1. Connect to RDS instance and run schema creation scripts from [database_schema.md](database_schema.md).

2. Enable Row-Level Security on all tenant-specific tables:
   ```sql
   ALTER TABLE users ENABLE ROW LEVEL SECURITY;
   CREATE POLICY users_tenant_isolation ON users 
   USING (tenant_id = current_setting('app.current_tenant_id')::INTEGER);
   ```

3. Create database indexes for performance.

### Data Migration Strategy

1. Use Flyway or Liquibase for version-controlled migrations.

2. Implement tenant-aware migration scripts.

3. Set up backup and restore procedures.

### Connection Pooling

1. Configure PgBouncer for connection pooling.

2. Set appropriate pool sizes based on application load.

## Security Configurations

### Authentication and Authorization

1. Implement JWT-based authentication in API Gateway.

2. Configure RBAC as per [rbac_design.md](rbac_design.md).

3. Set up OAuth2 integration for third-party logins.

### Data Encryption

1. Enable TLS 1.3 for all data in transit.

2. Use AWS KMS for data at rest encryption.

3. Implement field-level encryption for sensitive healthcare data.

### Network Security

1. Configure VPC security groups with least privilege access.

2. Set up AWS WAF for application layer protection.

3. Implement rate limiting and DDoS protection via Cloudflare.

### Compliance

1. Enable audit logging for all data access.

2. Implement HIPAA-compliant data handling.

3. Set up regular security scans and vulnerability assessments.

## Monitoring Setup

### Application Monitoring

1. Set up AWS CloudWatch for infrastructure monitoring.

2. Configure application logging with structured JSON logs.

3. Implement health check endpoints for all services.

### Performance Monitoring

1. Set up AWS X-Ray for distributed tracing.

2. Configure custom metrics for business KPIs.

3. Implement alerting for performance thresholds.

### Security Monitoring

1. Enable AWS GuardDuty for threat detection.

2. Set up CloudTrail for API activity monitoring.

3. Configure alerts for security events.

## Scaling Strategies

### Horizontal Scaling

1. Configure ECS service auto-scaling based on CPU/memory usage.

2. Implement database read replicas for read-heavy workloads.

3. Set up Redis cluster for cache scaling.

### Vertical Scaling

1. Monitor resource utilization and upgrade instance types as needed.

2. Implement database partitioning by tenant_id for large-scale deployments.

### Global Scaling

1. Use CloudFront for global content delivery.

2. Consider multi-region deployment for disaster recovery.

3. Implement data replication across regions.

## Tenant Onboarding Process

### Automated Provisioning

1. Create tenant record in database with subdomain.

2. Provision tenant-specific resources (S3 buckets, etc.).

3. Set up DNS records for subdomain routing.

4. Initialize default roles and permissions.

### Manual Configuration

1. Configure tenant branding and customization.

2. Set up initial user accounts and roles.

3. Import existing data if migrating from another system.

4. Configure integrations with external systems.

### Validation and Testing

1. Verify tenant isolation and data access controls.

2. Test authentication and authorization flows.

3. Validate subdomain routing and SSL certificates.

## Environment Variables

### Application Configuration
```
NODE_ENV=production
PORT=3000
DATABASE_URL=postgresql://user:password@rds-endpoint:5432/blink_eye
REDIS_URL=redis://cache-endpoint:6379
JWT_SECRET=<secure-jwt-secret>
API_GATEWAY_URL=https://api.blinkeye.com
```

### AWS Configuration
```
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=<access-key>
AWS_SECRET_ACCESS_KEY=<secret-key>
S3_DOCUMENTS_BUCKET=blink-eye-prod-documents
S3_IMAGES_BUCKET=blink-eye-prod-images
```

### Tenant Configuration
```
CURRENT_TENANT_ID=<tenant-id>
TENANT_SUBDOMAIN=<subdomain>
TENANT_BRANDING_CONFIG=<json-config>
```

### External Services
```
EMAIL_SERVICE_API_KEY=<api-key>
SMS_SERVICE_API_KEY=<api-key>
PAYMENT_GATEWAY_KEY=<gateway-key>
```

## Troubleshooting

### Common Issues

#### Database Connection Failures
- Verify security group rules allow access from application servers.
- Check database credentials and connection string.
- Ensure SSL certificates are properly configured.

#### Tenant Isolation Problems
- Verify RLS policies are correctly applied.
- Check that `app.current_tenant_id` is set in database session.
- Review application code for proper tenant context propagation.

#### Performance Issues
- Monitor database query performance and add indexes as needed.
- Check Redis cache hit rates and adjust cache strategies.
- Review application logs for bottlenecks.

#### SSL/TLS Errors
- Verify Cloudflare SSL configuration.
- Check certificate validity and renewal status.
- Ensure proper certificate chain is installed.

### Debugging Steps

1. Check application logs in CloudWatch.
2. Use AWS X-Ray for request tracing.
3. Monitor database performance with RDS Performance Insights.
4. Test API endpoints with tools like Postman.

### Support Contacts

- DevOps Team: devops@blinkeye.com
- Security Team: security@blinkeye.com
- Database Administrators: dba@blinkeye.com

---

This guide should be reviewed and updated as the platform evolves. Always test deployments in staging environments before production rollout.