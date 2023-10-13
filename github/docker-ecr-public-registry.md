## Interacting with ECR public registries

Ensure you set the `aws-region` to `us-east-1` as this is the only region that is available for ECR public registries. Anything else will fail.

```yml
- uses: aws-actions/configure-aws-credentials@v4
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: us-east-1

- uses: aws-actions/amazon-ecr-login@v2
  with:
    registry-type: public
```
