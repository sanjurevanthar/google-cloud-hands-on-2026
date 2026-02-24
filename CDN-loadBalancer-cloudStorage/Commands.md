# commands.md

## Part 1 – Create and Populate a Cloud Storage Bucket (GUI Steps)

1. In the Cloud Console, go to **Navigation Menu > Cloud Storage > Buckets**
2. Click **Create bucket**
3. Fill in the following fields:

| Field | Value |
|---|---|
| Name | Enter a globally unique name |
| Location type | `Multi-regional` |
| Location | Choose a location far from you (different continent) |

4. Click **Continue**
5. Under **Prevent public access**, uncheck **Enforce public access prevention on this bucket**
6. Click **Continue** → Click **Create**

---

## Part 2 – Copy an Image into the Bucket

In Cloud Shell, copy the sample image into your bucket:
```bash
gsutil cp gs://spls/gsp217/cdn/cdn.png gs://[your-storage-bucket]
```

Click **Refresh** on the Bucket details page to verify the file was copied.

---

## Part 3 – Make the Bucket Public (GUI Steps)

1. On the **Bucket details** page, click the **Permissions** tab
2. Click **Grant Access**
3. In **New principals**, type `allUsers` and select it from the dropdown
4. For the role, select **Cloud Storage > Storage Object Viewer**
5. Click **Save** → Click **Allow public access**
6. Click the **Objects** tab → Click **Copy URL** under Public access
7. Open a new browser tab and paste the URL to verify the image is accessible

---

## Part 4 – Create the HTTP Load Balancer with Cloud CDN (GUI Steps)

### Start the Load Balancer

1. Go to **Navigation Menu > View All Products > Network services > Load Balancing**
2. Click **+ Create load balancer**
3. Under **Type of load balancer**, select **Application Load Balancer (HTTP/HTTPS)** → Click **Next**
4. Leave all settings as default → Click **Configure**
5. Set **Load Balancer Name** to `cdn-lb`

### Configure the Backend

1. Click **Backend configuration**
2. Click **Backend services & backend buckets** dropdown → Click **Create a backend bucket**
3. Fill in the following:

| Field | Value |
|---|---|
| Name | `cdn-bucket` |
| Cloud Storage bucket | Browse and select your bucket |
| Enable Cloud CDN | ✅ Check this box |
| Client TTL | `1 minute` |
| Default TTL | `1 minute` |
| Maximum TTL | `1 minute` |

4. Click **Create**

### Configure the Frontend

1. Click **Frontend configuration**
2. Fill in the following:

| Field | Value |
|---|---|
| Protocol | `HTTP` |
| IP version | `IPv4` |
| IP address | `Ephemeral` |
| Port | `80` |

3. Click **Done**

### Finalize

1. Click **Review and finalize** → Review the backend and frontend settings
2. Click **Create** and wait for the load balancer to be created
3. Click on the name of the load balancer (`cdn-lb`)
4. Note the **IP address** of the load balancer — you will need it in the next step

---

## Part 5 – Verify CDN Caching

Store the load balancer IP address in an environment variable:
```bash
export LB_IP_ADDRESS=<Enter the IP address of the Load Balancer>
```

Run 3 consecutive HTTP requests and time each one:
```bash
for i in {1..3};do curl -s -w "%{time_total}\n" -o /dev/null http://$LB_IP_ADDRESS/cdn.png; done
```

Run the command again a few more times to generate enough logs:
```bash
for i in {1..3};do curl -s -w "%{time_total}\n" -o /dev/null http://$LB_IP_ADDRESS/cdn.png; done
```

---

## Part 6 – Explore CDN Logs (GUI Steps)

1. Go to **Navigation Menu > View all products > Observability > Logging > Logs Explorer**
2. Under the **Resources** filter, select:
   - **Application Load Balancer > cdn-lb-forwarding-rule > cdn-lb**
3. Click **Apply** → Click **Run Query**
4. Expand the **first log entry (top)** and check:
   - `httpRequest` → `cacheLookup: true`, no `cacheHit` field → **Cache MISS** (first request)
   - `jsonPayload` → `statusDetails: response_sent_by_backend`
5. Expand a **log entry near the bottom** and check:
   - `httpRequest` → `cacheLookup: true`, `cacheHit: true` → **Cache HIT**
   - `jsonPayload` → `statusDetails: response_from_cache`