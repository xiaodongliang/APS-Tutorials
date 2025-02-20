# Download File that is referenced with RFI

1. Assume RFI Id  is available (this can be fetched by RFI API or data from other workflows)
2. by 3-legged token, call Relationship API with the params that specify the main entity is RFI (domain = autodesk-bim360-rfi, type = rfi) and its id. The params also specify the referenced entity( domain = autodesk-bim360-documentmanagement,type=documentlineage), but without specifying the withId because this tutorial fetches all referenced files of this RFI.

```
   GET: https://developer.api.autodesk.com/bim360/relationship/v2/containers/:containerId/relationships:search?domain=autodesk-bim360-rfi&type=rfi&id=b963129e-c336-4707-a676-aa09f1d10f68&withdomain=autodesk-bim360-documentmanagement&withtype=documentlineage
```
containerId is ACC project id

The response will tell the array of the pairs of references. e.g.

```json
{
    "page": {
        "syncToken": "eyxxxx"
    },
    "relationships": [
        {
            "permission": null,
            "id": "620c2274-28b0-48c0-86d9-1db4ca0ddb10",
            "createdOn": "2025-02-20T05:19:54.936297+00:00",
            "isReadOnly": false,
            "isService": false,
            "isDeleted": false,
            "entities": [
                {
                    "createdOn": "2025-02-20T05:19:54.933142+00:00",
                    "domain": "autodesk-bim360-documentmanagement",
                    "type": "documentlineage",
                    "id": "urn:adsk.wipprod:dm.lineage:-MLKaFb9SGC4GEDZrPcGwg"
                },
                {
                    "createdOn": "2025-02-20T05:19:54.933142+00:00",
                    "domain": "autodesk-bim360-rfi",
                    "type": "rfi",
                    "id": "b963129e-c336-4707-a676-aa09f1d10f68"
                }
            ]
        }
    ]
}
```

3. from the response above, you will get the file lineage id (urn) **urn:adsk.wipprod:dm.lineage:-MLKaFb9SGC4GEDZrPcGwg**
4. by 2-legged or 3-legged token, get the storage id(urn) with this file lineage urn.

```
GET https://developer.api.autodesk.com/data/v1/projects/:project_id/items/:item_id
```
project_id is **b.<projectId>**
item_id id is the  file lineage urn

e.g.

```
GET: https://developer.api.autodesk.com/data/v1/projects/b.854c194f-05b3-4117-b926-69fa36c33c0c/items/urn:adsk.wipprod:dm.lineage:-MLKaFb9SGC4GEDZrPcGwg
```

The response will tell the general information of this file item. In the **included** node, it provides the data of the tip version. One sub node is **storage.data.id**. It tells the storage data of this tip version.

```json
{
    "jsonapi": {
        "version": "1.0"
    },
    "links": {
        "self": {
            "href": "https://developer.api.autodesk.com/data/v1/projects/b.854c194f-05b3-4117-b926-69fa36c33c0c/items/urn:adsk.wipprod:dm.lineage:-MLKaFb9SGC4GEDZrPcGwg"
        }
    },
    "data": {
        "type": "items",
        "id": "urn:adsk.wipprod:dm.lineage:-MLKaFb9SGC4GEDZrPcGwg",
        "attributes": {
            "displayName": "rac_basic_sample.rvt",
            "createTime": "2025-02-20T05:15:05.0000000Z",
            "createUserId": "200902260532621",
            "createUserName": "Xiao Dong Liang",
            "lastModifiedTime": "2025-02-20T05:15:05.0000000Z",
            "lastModifiedUserId": "200902260532621",
            "lastModifiedUserName": "Xiao Dong Liang",
            "hidden": false,
            "reserved": false,
            "extension": {
                "type": "items:autodesk.bim360:File",
                "version": "1.0",
                "schema": {
                    "href": "https://developer.api.autodesk.com/schema/v1/versions/items:autodesk.bim360:File-1.0"
                },
                "data": {
                    "sourceFileName": "rac_basic_sample.rvt"
                }
            }
        },
        "links": {
            "self": {
                "href": "https://developer.api.autodesk.com/data/v1/projects/b.854c194f-05b3-4117-b926-69fa36c33c0c/items/urn:adsk.wipprod:dm.lineage:-MLKaFb9SGC4GEDZrPcGwg"
            },
            "webView": {
                "href": "https://acc.autodesk.com/docs/files/projects/854c194f-05b3-4117-b926-69fa36c33c0c?folderUrn=urn%3Aadsk.wipprod%3Afs.folder%3Aco.rlsgK_qzQF-i86K2YvK8Og&entityId=urn%3Aadsk.wipprod%3Adm.lineage%3A-MLKaFb9SGC4GEDZrPcGwg"
            }
        },
        "relationships": {
            "tip": {
                "data": {
                    "type": "versions",
                    "id": "urn:adsk.wipprod:fs.file:vf.-MLKaFb9SGC4GEDZrPcGwg?version=1"
                },
               .....
        }
    },
    "included": [
        {
            "type": "versions",
            "id": "urn:adsk.wipprod:fs.file:vf.-MLKaFb9SGC4GEDZrPcGwg?version=1",
            "attributes": {
                "name": "rac_basic_sample.rvt",
                "displayName": "rac_basic_sample.rvt",
                "createTime": "2025-02-20T05:15:05.0000000Z",
                "createUserId": "200902260532621",
                "createUserName": "Xiao Dong Liang",
                "lastModifiedTime": "2025-02-20T05:18:59.0000000Z",
                "lastModifiedUserId": "200902260532621",
                "lastModifiedUserName": "Xiao Dong Liang",
                "versionNumber": 1,
                "storageSize": 19722240,
                "fileType": "rvt",
                "extension": {
                    "type": "versions:autodesk.bim360:File",
                    "version": "1.0",
                    "schema": {
                        "href": "https://developer.api.autodesk.com/schema/v1/versions/versions:autodesk.bim360:File-1.0"
                    },
                    "data": {
                        "processState": "PROCESSING_COMPLETE",
                        "extractionState": "SUCCESS",
                        "splittingState": "NOT_SPLIT",
                        "reviewState": "NOT_IN_REVIEW",
                        "revisionDisplayLabel": "1",
                        "sourceFileName": "rac_basic_sample.rvt",
                        "conformingStatus": "NONE"
                    }
                }
            },
            "links": {
               ....
                "derivatives": {
                    "data": {
                        "type": "derivatives",
                        "id": "dXJuOmFkc2sud2lwcHJvZDpmcy5maWxlOnZmLi1NTEthRmI5U0dDNEdFRFpyUGNHd2c_dmVyc2lvbj0x"
                    },
                    "meta": {
                        "link": {
                            "href": "https://developer.api.autodesk.com/modelderivative/v2/designdata/dXJuOmFkc2sud2lwcHJvZDpmcy5maWxlOnZmLi1NTEthRmI5U0dDNEdFRFpyUGNHd2c_dmVyc2lvbj0x/manifest?scopes=b360project.854c194f-05b3-4117-b926-69fa36c33c0c,O2tenant.14823638"
                        }
                    }
                }, 
                "storage": {
                    "data": {
                        "type": "objects",
                        "id": "urn:adsk.objects:os.object:wip.dm.prod/8b06d920-2eab-4a62-b5a9-058182cbfe4a.rvt"
                    },
                    "meta": {
                        "link": {
                            "href": "https://developer.api.autodesk.com/oss/v2/buckets/wip.dm.prod/objects/8b06d920-2eab-4a62-b5a9-058182cbfe4a.rvt?scopes=b360project.854c194f-05b3-4117-b926-69fa36c33c0c,O2tenant.14823638"
                        }
                    }
                }
            }
        }
    ]
}
````

5. Get bucket name and object name from the **storage.data.id**. e.g. **wip.dm.prod** is bucket name, **8b06d920-2eab-4a62-b5a9-058182cbfe4a.rvt** is object name. Do not hard code the bucket name. It can be different in different region or domain. Please check [this blog](https://aps.autodesk.com/blog/best-practice-uploading-and-downloading-files-bim360-docs) on how to extract bucket name object name:
   

6. With the bucket name and object name, call the API to get S3 signed url

````
  GET "https://developer.api.autodesk.com/oss/v2/buckets/wip.dm.prod/objects/8b06d920-2eab-4a62-b5a9-058182cbfe4a.rvt/signeds3download"
````
It will return the S3 signed url e.g.

```json
{
  "status": "complete",
  "url": "https://cdn.us.oss.api.autodesk.com/com.autodesk.oss-persistent/us-east-1/3f/bf/5f/0137a7d9dc53af930bc1b527320d50fb5f/wip.dm.prod?response-content-type=application%2Foctet-stream&response-content-disposition=attachment%3B+filename%3D%977d69b1-43e7-40fa-8ece-6ec4602892f3.rvt%22&Expires=1643864703&Signature=00PZYS6gL~Nc6aRG2HAhOCKYl0xtqsuujMJ~VKSXm1vBa-OxS4lPQBSlTx5bswpLBe1W6Rz94eIZW2sPN-v6Mzz~JyXNZ-V9Z7zlBoE1VoQhspLioC225hxq6ZmDSU5QnZXuNDV4ih~p1n3xacYvUvQWX-ONAGVUgQvZ253Svw~qx-pO4j-Yh4kVRmzDZqQut1xOI5ZGH6JFGhXLSzkgbYcfYx6fvCxnvYUJrgAcqncIwGVewI3uC0I84Fzrj8nXE8ojuojqJP0pNlxkfBe~2LfjjzqKDKaNvfC2Grt12j9QgC~cN7nQCRcVUhExpoV1VVB5x3AkVTJ-q5NoedvsfO__&Key-Pair-Id=95HRZD7MMO1UK",
  "params": {
    "content-type": "application/octet-stream",
    "content-disposition": "attachment; filename=\"977d69b1-43e7-40fa-8ece-6ec4602892f3.rvt\""
  },
  "size": 472424,
  "sha1": "ffbf5f0137a7d9dc53af930bc1b527320d50fb53"
}
```
7. call the url above to start downloading the binary file. you can also specify the range to [download pieces by pieces and finally merge them on local](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.35)
 
