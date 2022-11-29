## Kiali 

> References: https://kiali.io/docs/



Kiali is a console for Istio service mesh.  Kiali  can be quickly installed as an Istio add-on, or trusted as a part of  your production environment.

Features:

- Application Wizards - provides Actions to create, update and delete Istio configuration, driven by wizards.
- Detail Views - provides list and detail views for your mesh components.
- Health - helps users know whether their service mesh is healthy. This includes the health of the mesh infrastructure itself, and the deployed application services.
- Istio Configuration - helps you to configure, update and validate your Istio service mesh.
- Istio Infrastructure Status - monitors the multiple components that make up the service mesh, letting you know if there is an underlying problem.
- Multi-cluster Deployment - advanced Mesh Deployment and Multi-cluster support.
- Security - gives support to better understand how mTLS is used in Istio meshes. Find those helpers in the graph, the masthead menu, the overview page and specific validations.
- Topology - offers multiple ways for users to examine their mesh Topology.  Each  combines several information types to help users quickly evaluate the  health of their service architecture.
- Tracing - offers a native integration with Jaeger for Distributed Tracing.
- Validation - performs a set of validations on your Istio Objects, such as Destination Rules, Service Entries, and Virtual Services.

Architecture:

![Kiali architecture](.kiali-images/architecture.png)

