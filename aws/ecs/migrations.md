## AWS ECS - Running migrations

The `aws-actions/amazon-ecs-deploy-task-definition` GitHub Actions task does not allow for running one-off tasks currently. There is an [open ticket regarding this](https://github.com/aws-actions/amazon-ecs-deploy-task-definition/issues/54). As mentioned in the issue, a good workaround for now is to add an extra container that runs the migrations, and have your main application list this as a dependency that must run successfully:

```json
{
  "containerDefinitions": [
    {
      "name": "app-container",
      "image": "{my-image}:latest",
      "essential": true,
      "dependsOn": [
        {
          "containerName": "migration",
          "condition": "SUCCESS"
        }
      ]
    },
    {
      "name": "migration",
      "image": "{my-image}:latest",
      "essential": false, 
      "command":[
         "npm",
         "run",
         "migrate"
      ]
   }
  ]
}
```

In the GitHub Actions workflow, we update the image value for each of the containers. It's important to note how we chain those together with the ID reference:

```yml
- name: Render Amazon ECS task definition for app
  id: task-def-app
  uses: aws-actions/amazon-ecs-render-task-definition@v1
  with:
    task-definition: ${{ env.TASK_DEFINITION }}
    container-name: app-container
    image: ${{ steps.build-image.outputs.image }}

- name: Render Amazon ECS task definition for migration
  id: task-def-migration
  uses: aws-actions/amazon-ecs-render-task-definition@v1
  with:
    task-definition: ${{ steps.task-def-app.outputs.task-definition }}
    container-name: migration
    image: ${{ steps.build-image.outputs.image }}

- name: Deploy Amazon ECS task definition
  uses: aws-actions/amazon-ecs-deploy-task-definition@v1
  with:
    task-definition: ${{ steps.task-def-migration.outputs.task-definition }}
    service: ${{ env.ECS_SERVICE }}
    cluster: ${{ env.ECS_CLUSTER }}
    wait-for-service-stability: true
```
