# Typeorm groupBy getManyAndCount

1. first, we need to construct queryBuidler & some complex condition subQuery
2. second, we need to use `subQuery` counting as below

```ts
const { totalItems } = await this.connection.rawConnection
  .createQueryBuilder()
  .select(`COUNT(1)`, 'totalItems')
  .from(`(${queryBuilder.getQuery()})`, 'orderline')
  .setParameters(queryBuilder.getParameters())
  .getRawOne();
```

3. using `getRawMany()` to fetch minimal fields (`GROUP_CONCAT(orderline.id)` as `orderLineIds`)
4. refetch all record using `orderLineIds`

### The example as below.

```ts
async availableOrderLinesToReview(
    ctx: RequestContext,
    customer: Customer,
    options?: Pick<ListQueryOptions<OrderLine>, 'take' | 'skip'>
  ): Promise<PaginatedList<ReviewOrderLine>> {
    const customerReviewQb = this.getCustomerReviewedOrderLineQueryBuilder(
      ctx,
      customer
    );

    const queryBuilder = this.connection
      .getRepository(ctx, OrderLine)
      .createQueryBuilder('orderline')
      .leftJoin('orderline.order', 'order')
      .select(`GROUP_CONCAT( orderline.id )`, 'orderLineIds')
      .where('order.customerId = :customerId', { customerId: customer.id })
      .andWhere('order.active = :active', { active: false })
      .andWhere('order.state IN (:...orderStates)', {
        orderStates: orderFinishedState,
      })
      .andWhere(`orderline.id NOT IN (${customerReviewQb.getQuery()})`)
      .setParameters(customerReviewQb.getParameters())
      .groupBy('orderline.orderId')
      .orderBy('order.orderPlacedAt', 'DESC');

    const { totalItems } = await this.connection.rawConnection
      .createQueryBuilder()
      .select(`COUNT(1)`, 'totalItems')
      .from(`(${queryBuilder.getQuery()})`, 'orderline')
      .setParameters(queryBuilder.getParameters())
      .getRawOne();

    const rowItems = await queryBuilder
      .offset(options?.skip)
      .limit(options?.take)
      .getRawMany();

    const orderLineIds = rowItems.reduce<string[]>((prev, curr) => {
      return prev.concat(curr['orderLineIds'].split(','));
    }, []);

    const orderLineItems = await this.connection
      .getRepository(ctx, OrderLine)
      .find({
        where: {
          id: In(unique(orderLineIds)),
        },
        relations: [
          'order',
          'productVariant',
          'productVariant.featuredAsset',
          'productVariant.product',
        ],
        order: {
          updatedAt: 'DESC',
        },
      });

    const newItems: ReviewOrderLine[] = orderLineItems.map((s) => {
      const variant =
        s.productVariant &&
        this.translator.translate(s.productVariant, ctx, ['product']);

      return {
        id: s.id,
        orderLineId: s.id,
        orderId: s.order.id,
        productId: variant.productId,
        productVariantId: variant.id,
        productSlug: variant.product.slug,
        productVariantSku: variant.sku,
        productVariantName: variant.name,
        productVariantAsset: variant.featuredAsset,
      };
    });

    return {
      items: newItems,
      totalItems,
    };
  }
```
