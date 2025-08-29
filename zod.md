## Zod

Use zod to create schemas and types that needs to be used for tool inputs or Spiceflow API routes.

When you need to create a complex type that comes from Prisma table do not create a new schema that tries to recreate the Prisma table structure, instead just use `z.any() as ZodType<PrismaTable>)` to get type safety but leave any in the schema. This gets most of the benefits of Zod without having to define a new Zod schema that can easily go out of sync.