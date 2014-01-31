---
layout: default
categories: blog
tags: postgres db
title: Killing orphans
---

No, this post it not about being really nasty and killing some poor parentless children, it is about
deleting database records that referer to other records that nolonger exist... anyway, to the point..

I found some entries in our database that were orphans which was breaking some ruby code .. guess this
happened as someone deleted record directly in the database without using ruby and activerecord to delete
the related entries...  so I cleaned them up like so:

    DELETE FROM vehicle_pictures WHERE id IN (SELECT vpp.id FROM vehicle_pictures vpp JOIN (SELECT vp.*, v.id AS vid FROM vehicle_pictures vp LEFT OUTER JOIN vehicles v ON vp.vehicle_id = v.id WHERE v.id IS NULL) oph ON vpp.id=oph.id);

There is likely a cleaner way, but this worked.
