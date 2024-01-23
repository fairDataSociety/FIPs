- FIP: TBD
- title: Creator Pod Marketplace
- author: Vladan Tomic (@tomicvladan)
- status: draft
- created: 22.01.2024

# Introduction

The creator pods are pods that are shared for other users, but access requires paying a fee to the publisher.

This document describes how creator pods can be integrated into the Fairdrive. That will enable content creators to easily publish and advertise pods that they want to offer for subscription. On the other end, all users will be able to browse and subscribe to content they are interested in.

# UI changes

Adding new options to the drive page would make too much clutter. Instead it's better to create a completely separate page for creator pods. But the drive page can be extended to show not only the user's pods, but the subscribed pods as well. The only difference between user's pods and subscribed pods will be that the subscribed pods won't have options for adding files and folders. Instead they will be read-only.

The page for creator pods can be divided into subpages or tabs for better accessibility. Because the majority of the users will use the page to subscribe to existing creator pods, the initial content of the page should be adjusted for that purpose. The user can choose a category and subcategories to filter pods. For each pod listed, there will be an option to see details of the pod. The details will include information about the creator, terms and price for subscription. That information will be shown in a modal dialog, with a button for subscription.

Initially there could be displayed pods with the most number of subscribers and additionally some random available pods.

In a separate tab or subpage, the user will be able to see pending subscription requests.

For pod publishers there will be a separate tab with a list of their pods. Selecting a pod from the list will open additional details in a modal dialog. If the pod is not yet published there will be a button which will publish the pod to the marketplace. For those pods that are already published there will be an option to unpublish them. Also there will be a list of all subscribers as well as pending requests. There the user will be able to cancel subscriptions or allow new subscriptions.

# Implementation

All the required functions to interact with the DataHub smart contract and work with subscription pods will be implemented in the [fdp-storage](https://github.com/fairDataSociety/fdp-storage) library.

The implementation in the Fairdrive will include creating a new page for creator pods and extending the existing drive page with subscribed pods.

Interaction with the smart contract will require the Metamask extension to be available.

For listing subscribed pods in the drive page and in the creator page, the `getAllSubItems` and `openSubscribedPod` function will be used. The `getAllSubItems` functtion fetches pod locations from the smart contract and the `openSubscribedPod` function decrypts pod information. With that data, pod names can be displayed in the UI. These two functions can be combined inside the `fdp-storage` to provide an even more robust interface. Once when shared pod data is retrieved, shared pods can be opened as any other pod. This might require changes in the `fdp-storage` interface so custom pod address can be passed to the functions that work with files and folders.

To address a potential problem of speed and efficiency, there should be a maximum number of subscribed pods that are loaded initially. The user can request to load more by clicking on a button, or optionally pagination could be implemented.

In the creator page, most of the functionalities are interaction with the smart contract, so for that purpose the [fdp-contracts](https://github.com/fairDataSociety/fdp-contracts) library can be used directly. The `fdp-contract` library is exposed in the `fdp-storage`, so it will be instantly available.

The only action that requires storing data on Swarm is selling a subscription request. For that purpose, a new function needs to be implemented in the `fdp-storage` that will do the inverse job of the `openSubscribedPod`. That function will ecrypt pod information and update the contract with that information.

To avoid performance issues, all content that potentially can require a large number of requests should be either paginated or partially loaded. This applies for loading published pod information. For other use cases, data is loaded for individual pods so it won't cause too many requests.

# Time estimation

A rough estimation of time required to implement all features described in this document is around three weeks for one person.
